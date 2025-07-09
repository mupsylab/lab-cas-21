<template>
    <div id="exp"></div>
</template>

<script setup lang="ts">
import { h, onMounted } from 'vue';
import { JsPsych } from '../../utils/jsPsych/jsPsych';
import { TimelineArray } from '../../utils/jsPsych/timeline';

import Preload from '../../component/plugin/Preload.vue';
import HtmlKeyboard from '../../component/plugin/HtmlKeyboard.vue';
import CoreScreen from './component/CoreScreen.vue';
import EnterFullScreen from '../../component/plugin/EnterFullScreen.vue';
import Instruction from './component/Instruction.vue';
import HtmlButton from '../../component/plugin/HtmlButton.vue';

const jspsych = JsPsych.instance;
const timeline: TimelineArray = [];

timeline.push({
    component: h(Preload, {
        assets: [
            ["i_bg", "./assets/img/background.jpg"],
            ["i_bag", "./assets/img/bag_euro.png"],
            ["i_bag_n", "./assets/img/bag_neutral.png"],
            ["i_clouds", "./assets/img/clouds.png"],
            ["i_helicopter", "./assets/img/helicopter.png"],
            ["i_speaker", "./assets/img/Speaker_Icon.png"],
            ["a_cashreg", "./assets/audio/cashreg.mp3"],
            ["a_falling", "./assets/audio/falling.mp3"],
            ["a_trombone", "./assets/audio/trombone.mp3"]
        ]
    })
});

timeline.push({
    component: h(Instruction)
});

timeline.push({
    component: h(EnterFullScreen, {
        model: true,
        msg: "接下来你将正式开始实验。注意正式实验过程中, 会出现多云, 遮住直升机，这是正常的。",
        button_label: "点击进入全屏"
    })
});

timeline.push({
    timeline: [{
        component: h(HtmlButton, {
            stimulus: "接下来，你会进入练习阶段。",
            choices: ["开始练习"],
        })
    }, {
        timeline: [{
            component: h(HtmlKeyboard, {
                stimulus: "+",
                choices: ["NO_KEYS"],
                trial_duration_time: 500,
            })
        }, {
            component: function () {
                const { location = 25, reward = true, showCloud = false } = jspsych.currTrial.parent.getAllTimelineVariables()
                return h(CoreScreen, {
                    location: location,
                    reward: reward,
                    showCloud: showCloud
                })
            }
        }],
        timeline_variables: (function () {
            const res = [];
            const locations = [42, 45, 50, 44, 45];
            const rewards = [1, 0, 1, 1, 1];
            for (let i = 0; i < locations.length; i++) {
                res.push({
                    location: locations[i],
                    reward: rewards[i] === 1,
                    showCloud: true
                });
            }
            return res;
        })(),
    }]
});

timeline.push({
    timeline: [{
        component: h(HtmlButton, {
            stimulus: "接下来，你会进入正式实验阶段。",
            choices: ["开始"],
        })
    }, {
        timeline: [{
            component: h(HtmlKeyboard, {
                stimulus: "+",
                choices: ["NO_KEYS"],
                trial_duration_time: 500,
            })
        }, {
            component: function () {
                const { location = 25, reward = true, showCloud = false } = jspsych.currTrial.parent.getAllTimelineVariables()
                return h(CoreScreen, {
                    location: location,
                    reward: reward,
                    showCloud: showCloud
                })
            }
        }],
        timeline_variables: (function () {
            const res = [];
            const locations = [28, 20, 19, 100];
            const rewards = [1, 1, 0, 0];
            for (let i = 0; i < locations.length; i++) {
                res.push({
                    location: locations[i],
                    reward: rewards[i] === 1,
                    showCloud: true
                });
            }
            return res;
        })(),
    }]
});

timeline.push({
    timeline: [{
        component: h(HtmlButton, {
            stimulus: "请休息一会。休息结束后，点击下面的按钮继续。",
            choices: ["继续"],
        })
    }, {
        timeline: [{
            component: h(HtmlKeyboard, {
                stimulus: "+",
                choices: ["NO_KEYS"],
                trial_duration_time: 500,
            })
        }, {
            component: function () {
                const { location = 25, reward = true, showCloud = false } = jspsych.currTrial.parent.getAllTimelineVariables()
                return h(CoreScreen, {
                    location: location,
                    reward: reward,
                    showCloud: showCloud
                })
            }
        }],
        timeline_variables: (function () {
            const res = [];
            const locations = [90, 100];
            const rewards = [1, 0];
            for (let i = 0; i < locations.length; i++) {
                res.push({
                    location: locations[i],
                    reward: rewards[i] === 1,
                    showCloud: true
                });
            }
            return res;
        })(),
    }]
});

timeline.push({
    component: h(HtmlKeyboard, {
        stimulus: `
<div style="font-size: 48px; line-height: 96px;">实验结束</div>
<div style="font-size: 24px; color: var(--font-desc);">&copy; Mupsy 技术支持</div>`,
        choices: ["NO_KEYS"]
    }),
    on_load() {
        // 在实验结束时自动下载数据
        const blob = new Blob([jspsych.data.get().csv()], { type: 'text/csv;charset=utf-8;' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'experiment_data.csv';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
    }
});

onMounted(() => {
    const expDom = document.querySelector("#exp") as HTMLDivElement;
    jspsych.load(timeline, expDom);
});
</script>