<script setup lang="ts">
import { ref } from 'vue';
import CoreScreen from './CoreScreen.vue';
import { ElButton } from 'element-plus';
import { useLoaderAssets } from '../../../store/loadAssetsToBlob';
import Questionnaire from '../../../component/questionnaire/Questionnaire.vue';
import { JsPsych } from '../../../utils/jsPsych/jsPsych';

const loader = useLoaderAssets();
const pageLen = 7; // 页面数量
const page = ref(0);
function click(next: boolean) {
    if (next) {
        if (page.value < pageLen - 1) {
            page.value++;
        } else {
            // 如果是最后一页，可能需要执行一些结束操作
            console.log("实验结束");
        }
    } else {
        if (page.value > 0) {
            page.value--;
        }
    }
}
function end(obj: Record<string, any>) {
    // 结束实验的逻辑
    if (obj.q1 === '接住钱袋子' && obj.q2 === '直升机的位置以及风力') {
        JsPsych.instance.currTrial.finish({
            trial_type: 'instruction'
        });
    } else {
        alert("请重新开始实验，回答正确的选项。");
        page.value = 0; // 重置页面
    }
}
</script>

<template>
    <div class="box">
        <div class="display" v-if="page === 0">
            <p>欢迎您参与实验</p>
            <p>现在我们将解释游戏的玩法。点击屏幕<strong>左侧</strong>进入上一页，点击屏幕<strong>右侧</strong>进入下一页。</p>
            <div class="button-box">
                <ElButton @click="click(true)">下一页</ElButton>
            </div>
        </div>
        <div class="display" v-if="page === 1">
            <p>感谢您的参与！如果您在窗口中看不到鼠标光标，不必感到惊讶。没关系，游戏过程中您不需要它。</p>
            <p>请不要重新加载页面或使用浏览器的箭头按钮，因为这会终止实验并阻止您获得奖励。另外，请勿在第二个标签页中打开链接，并确保在整个实验过程中保持浏览器窗口全屏。</p>
            <p>请在完成任务时保持注意力集中。否则，我们必须将您排除在实验之外，您可能无法获得奖励。</p>
            <div class="button-box">
                <ElButton @click="click(false)">上一页</ElButton>
                <ElButton @click="click(true)">下一页</ElButton>
            </div>
        </div>
        <div class="display" v-if="page === 2">
            <p>稍后你会看到一架直升机。直升机会掉落一些装有钱的袋子。你的任务是接住它们。</p>
            <p>尽量赢得尽可能多的钱。</p>
            <p>你赢得的钱将在实验结束后转化为实际的报酬。</p>
            <div class="button-box">
                <ElButton @click="click(false)">上一页</ElButton>
                <ElButton @click="click(true)">下一页</ElButton>
            </div>
        </div>
        <div class="display" v-if="page === 3 || page === 4">
            <CoreScreen :prac="true" />
            <div class="text-box" v-if="page === 3">
                <p>现在我们来练习一下。</p>
                <p>移动鼠标，可以让坐标轴进行移动。你可以用坐标轴接住从飞机上掉落的物品。</p>
                <p>接下来请将坐标轴移动至飞机的正下方。</p>
                <p>然后按下鼠标左键。然后光标会停止移动，并且物品开始下落。</p>
                <p>尝试一下，然后进入下一步。</p>
                <div class="button-box">
                    <ElButton @click="click(false)">上一页</ElButton>
                    <ElButton @click="click(true)">下一页</ElButton>
                </div>
            </div>
            <div class="text-box" v-if="page === 4">
                <p>在点击后，你会看到物品掉落的中心位置（红色坐标轴）以及您所选择的位置之间的偏差（红线）。</p>
                <p>在3秒后，会自动结束。</p>
                <div class="button-box">
                    <ElButton @click="click(false)">上一页</ElButton>
                    <ElButton @click="click(true)">下一页</ElButton>
                </div>
            </div>
        </div>
        <div class="display" v-if="page === 5">
            <p>直升机会掉落沙袋或者钱袋，这是随机的。</p>
            <div>
                <img :src="loader.getAssets('i_bag_n')" alt="钱袋" style="width: 50px; height: 50px; margin-right: 20px;">
                <img :src="loader.getAssets('i_bag')" alt="沙袋" style="width: 50px; height: 50px;">
            </div>
            <p>如果你接住了钱袋，你会获得奖励；如果接住了沙袋，你将不会获得奖励。</p>
            <p>但请无论如何都尽力接住它们。</p>
            <p>物品的落点会有所不同，这取决于飞机的位置，以及风力大小。</p>
            <div class="button-box">
                <ElButton @click="click(false)">上一页</ElButton>
                <ElButton @click="click(true)">下一页</ElButton>
            </div>
        </div>
        <div class="display" v-if="page === 6">
            <p>请回答下列问题</p>
            <Questionnaire :ques="[
                { name: 'q1', type: 'radio', title: '如果我_____, 则可以取得额外的奖励。', choices: ['点击直升机', '接住钱袋子', '尽快做出反应', '接住下落的物品'] },
                { name: 'q2', type: 'radio', title: '下落的物品位置取决于。', choices: ['直升机的位置', '物品是沙袋还是钱袋', '之前是否抓住物品', '直升机的位置以及风力'] }
            ]" @end-trial="end"></Questionnaire>
        </div>
    </div>
</template>

<style lang="css" scoped>
.display {
    font-size: 16px;
    line-height: 24px;
    text-indent: 2em;
    user-select: none;
}

.text-box {
    display: inline-block;
    width: 75vw;
    position: absolute;
    transform: translate(-60%, -80%);
    color: black;
}

.button-box {
    text-align: center;
}
</style>