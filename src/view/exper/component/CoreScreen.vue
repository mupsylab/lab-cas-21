<script lang="ts" setup>
import { onMounted, ref } from 'vue';
import { useLoaderAssets } from '../../../store/loadAssetsToBlob';
import { JsPsych } from '../../../utils/jsPsych/jsPsych';
import { useCheckBrowserInfo } from '../../../store/browserCheck';
import { ElMessage } from 'element-plus';

const props = defineProps({
    location: {
        type: Number,
        default: 25
    },
    reward: {
        type: Boolean,
        default: true
    },
    showCloud: {
        type: Boolean,
        default: false
    },
    prac: {
        type: Boolean,
        default: false
    }
});
const loader = useLoaderAssets();
const isTouch = useCheckBrowserInfo().browser.isTouch;
/**
 * 进度条
 */
const pregress = ref(0);
/**
 * 反应时间
 */
let rt = -1;
/**
 * 包裹下降的高度
 * @type {number}
 */
let fall: number = 0;
/**
 * 是否点击了鼠标
 * @type {boolean}
 */
let isClick: boolean = false;
/**
 * 有效碰撞体积
 */
const invalidArea = {
    w: 304 / 4,
    h: 406 / 4
};

const [width, height] = [window.innerWidth, window.innerHeight];
const [l, r, t, b] = [0.1, 0.9, 0.6, 0.7];
const [x1, x2, y1, y2] = [width * l, width * r, height * t, height * b];

const i_helicopter = new Image();
i_helicopter.src = loader.getAssets("i_helicopter");
const i_clouds = new Image();
i_clouds.src = loader.getAssets("i_clouds");
const i_bg = new Image();
i_bg.src = loader.getAssets("i_bg");
const i_bag = new Image();
i_bag.src = loader.getAssets("i_bag");
const i_bag_n = new Image();
i_bag_n.src = loader.getAssets("i_bag_n");

const a_cashreg = new Audio(loader.getAssets("a_cashreg"));

function drawHelicopter(ctx: CanvasRenderingContext2D) {
    const x = x1 + (x2 - x1) * props.location / 100;
    const y = height * 0.1
    ctx.drawImage(i_helicopter, 0, 0, i_helicopter.width, i_helicopter.height, x - i_helicopter.width / 8, y - i_helicopter.height / 8, i_helicopter.width / 4, i_helicopter.height / 4);
}
function drawBackground(ctx: CanvasRenderingContext2D) {
    ctx.drawImage(i_bg, 0, 0, ctx.canvas.width, ctx.canvas.height);
}
function drawAxis(ctx: CanvasRenderingContext2D) {
    ctx.strokeStyle = '#fff';
    ctx.lineWidth = 4;
    ctx.beginPath();
    // 左边的轴
    ctx.moveTo(x1, y1);
    ctx.lineTo(x1, y2);
    // 右边的轴
    ctx.moveTo(x2, y1);
    ctx.lineTo(x2, y2);
    // 中间的轴
    ctx.moveTo(x1, (y1 + y2) / 2);
    ctx.lineTo(x2, (y1 + y2) / 2);
    ctx.stroke();
}
function drawCursor(ctx: CanvasRenderingContext2D) {
    const x = pregress.value / 100 * (x2 - x1) + x1;
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 4;
    ctx.beginPath();
    ctx.moveTo(x, y1);
    ctx.lineTo(x, y2);
    ctx.stroke();
}
function drawCloud(ctx: CanvasRenderingContext2D) {
    ctx.drawImage(i_clouds, 0, 0, i_clouds.width, i_clouds.height, 0, 0, width, height * 0.4);
}
function drawBag(ctx: CanvasRenderingContext2D) {
    const x = x1 + (x2 - x1) * props.location / 100;
    const y = height * 0.1 + fall;
    if (props.reward) {
        ctx.drawImage(i_bag, 0, 0, i_bag.width, i_bag.height, x - i_bag.width / 8, y - i_bag.height / 8, i_bag.width / 4, i_bag.height / 4);
    } else {
        ctx.drawImage(i_bag_n, 0, 0, i_bag_n.width, i_bag_n.height, x - i_bag_n.width / 8, y - i_bag_n.height / 8, i_bag_n.width / 4, i_bag_n.height / 4);
    }
    if (!isClick && (y + invalidArea.h / 2 >= y1 && Math.abs(x - (pregress.value / 100 * (x2 - x1) + x1)) < invalidArea.w / 2)) {
        // 如果包裹落在了目标区域内
        JsPsych.plugin.timer.clearAllTimer();
        a_cashreg.play();
        JsPsych.plugin.timer.setTimeout(() => {
            if (!props.prac) JsPsych.instance.currTrial.finish({
                trail_type: "bag_fall",
                location: props.location,
                reward: props.reward,
                show_cloud: props.showCloud,
                pregress: pregress.value,
                win: true,
                rt, isTouch
            });
            else reset();
        }, 3000);
        isClick = true;
    }
    if (!isClick && y + invalidArea.h / 2 >= height) {
        // 如果包裹落在了地面上
        JsPsych.plugin.timer.clearAllTimer();
        if (!isClick) JsPsych.plugin.timer.setTimeout(() => {
            if (!props.prac) JsPsych.instance.currTrial.finish({
                trail_type: "bag_fall",
                location: props.location,
                reward: props.reward,
                show_cloud: props.showCloud,
                pregress: pregress.value,
                win: false,
                rt, isTouch
            });
            else reset();
        }, 3000);
        isClick = true;
    }
}
function drawTarget(ctx: CanvasRenderingContext2D) {
    const xt = props.location / 100 * (x2 - x1) + x1;
    const xp = pregress.value / 100 * (x2 - x1) + x1;
    ctx.strokeStyle = '#f00';
    ctx.lineWidth = 4;
    ctx.beginPath();
    ctx.moveTo(xt, y1);
    ctx.lineTo(xt, y2);
    ctx.moveTo(xt, (y1 + y2) / 2);
    ctx.lineTo(xp, (y1 + y2) / 2);
    ctx.stroke();
}

function frame(ctx: CanvasRenderingContext2D) {
    if (isClick) return;
    drawBackground(ctx);
    drawBag(ctx);
    drawHelicopter(ctx);
    if (props.showCloud) drawCloud(ctx);
    drawAxis(ctx);
    if (isClick) drawTarget(ctx);
    drawCursor(ctx);
    requestAnimationFrame(() => frame(ctx));
}
onMounted(() => {
    reset();
});
function reset() {
    pregress.value = 0;
    fall = 0;
    isClick = false;
    rt = -1;

    const canvas = document.querySelector('#show') as HTMLCanvasElement;
    const ctx = canvas.getContext('2d') as CanvasRenderingContext2D;
    canvas.width = width;
    canvas.height = height;
    frame(ctx);

    JsPsych.plugin.pointer.addListener(handler);
    JsPsych.plugin.timer.setTimeout(() => {
        JsPsych.plugin.pointer.removeListener(handler);
        JsPsych.plugin.timer.setInterval(() => {
            fall += 4;
        }, 10);
        ElMessage.error("反应太慢, 请快一点");
    }, 3000); // 3秒超时
}

function handler(e: PointerEvent, time: number) {
    if (isTouch ? e.type === "pointerleave" : e.type === "pointerdown") {
        rt = JsPsych.instance.currTrial.getIntervalTime(time);
        JsPsych.plugin.pointer.removeListener(handler);
        JsPsych.plugin.timer.setInterval(() => {
            fall += 4;
        }, 10);
        return; // 只在 pointermove 时更新进度
    }
    const { x } = e;
    const x1 = width * l;
    const x2 = width * r;

    const t = (x - x1) / (x2 - x1) * 100;
    if (t < 0) {
        pregress.value = 0;
    } else if (t > 100) {
        pregress.value = 100;
    } else {
        pregress.value = t;
    }
}
</script>

<template>
    <div class="box">
        <canvas id="show"></canvas>
    </div>
</template>

<style lang="css" scoped>
.box canvas {
    position: absolute;
    top: 0;
    left: 0;
    transform: translate(-50%, -50%);
}
</style>