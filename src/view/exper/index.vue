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
            const locations = [10, 15, 12, 20, 18, 15, 70, 72, 80, 75, 78, 81, 75, 40, 41, 42, 45, 50, 44, 45];
            const rewards = [0, 0, 0, 1, 1, 0, 1, 0, 1, 0, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1];
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
            const locations = [
                71,
                72,
                77,
                76,
                74,
                88,
                78,
                85,
                57,
                53,
                56,
                56,
                62,
                51,
                50,
                60,
                8,
                13,
                12,
                17,
                15,
                10,
                17,
                9,
                10,
                10,
                10,
                13,
                16,
                14,
                11,
                9,
                5,
                9,
                10,
                14,
                11,
                12,
                9,
                60,
                61,
                55,
                53,
                53,
                49,
                50,
                54,
                53,
                48,
                47,
                52,
                77,
                75,
                70,
                73,
                76,
                76,
                77,
                68,
                76,
                72,
                71,
                71,
                73,
                23,
                24,
                27,
                28,
                20,
                19,
                100
            ];
            const rewards = [
                1,
                0,
                0,
                1,
                1,
                1,
                0,
                0,
                1,
                0,
                1,
                1,
                0,
                0,
                1,
                1,
                0,
                1,
                1,
                0,
                1,
                0,
                1,
                0,
                0,
                1,
                1,
                0,
                1,
                0,
                0,
                1,
                1,
                1,
                0,
                1,
                1,
                1,
                0,
                0,
                1,
                0,
                0,
                1,
                1,
                0,
                1,
                1,
                1,
                1,
                0,
                0,
                1,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                1,
                0,
                1,
                1,
                0,
                0,
                0,
                1,
                1,
                0,
                0
            ];
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
            const locations = [
                63,
                35,
                52,
                39,
                29,
                38,
                52,
                43,
                54,
                45,
                55,
                58,
                49,
                46,
                44,
                44,
                46,
                52,
                85,
                82,
                74,
                87,
                88,
                100,
                93,
                82,
                83,
                89,
                71,
                72,
                81,
                73,
                88,
                93,
                87,
                87,
                87,
                77,
                93,
                14,
                15,
                16,
                21,
                19,
                11,
                13,
                31,
                18,
                95,
                70,
                73,
                85,
                62,
                81,
                82,
                86,
                85,
                74,
                75,
                83,
                65,
                77,
                85,
                72,
                67,
                84,
                85,
                66,
                72,
                90,
                100
            ];
            const rewards = [
                0,
                0,
                0,
                0,
                0,
                0,
                0,
                0,
                0,
                1,
                1,
                1,
                0,
                0,
                0,
                1,
                1,
                1,
                0,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                1,
                0,
                1,
                0,
                1,
                0,
                0,
                0,
                1,
                1,
                0,
                1,
                0,
                1,
                1,
                1,
                0,
                0,
                0,
                0,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                0,
                0,
                1,
                1,
                1,
                0,
                0,
                1,
                1,
                1,
                1,
                0,
                1,
                0,
                0,
                0,
                1,
                0,
            ];
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
            const locations = [
                69,
                73,
                68,
                68,
                67,
                68,
                20,
                14,
                19,
                21,
                24,
                19,
                16,
                61,
                57,
                61,
                60,
                55,
                56,
                59,
                58,
                61,
                59,
                59,
                56,
                60,
                64,
                56,
                61,
                58,
                59,
                58,
                58,
                54,
                54,
                39,
                42,
                43,
                44,
                32,
                34,
                40,
                40,
                41,
                40,
                36,
                36,
                74,
                78,
                78,
                78,
                74,
                72,
                74,
                78,
                79,
                78,
                76,
                74,
                82,
                74,
                74,
                80,
                78,
                75,
                76,
                68,
                77,
                76,
                75,
                100
            ];
            const rewards = [
                1,
                1,
                0,
                1,
                1,
                1,
                0,
                0,
                0,
                1,
                1,
                1,
                0,
                0,
                0,
                1,
                0,
                0,
                1,
                0,
                1,
                0,
                0,
                1,
                0,
                0,
                0,
                1,
                0,
                0,
                0,
                0,
                1,
                0,
                1,
                1,
                0,
                1,
                1,
                0,
                1,
                0,
                1,
                0,
                0,
                1,
                0,
                1,
                0,
                0,
                0,
                1,
                0,
                0,
                1,
                0,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                0,
                1,
                1,
                1,
                0,
                1,
                0,
                0
            ];
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
            const locations = [
                99,
                87,
                80,
                52,
                74,
                89,
                21,
                41,
                36,
                52,
                46,
                49,
                35,
                44,
                43,
                38,
                19,
                44,
                45,
                30,
                48,
                36,
                46,
                49,
                35,
                40,
                14,
                17,
                21,
                15,
                35,
                10,
                15,
                13,
                29,
                42,
                47,
                38,
                38,
                24,
                28,
                49,
                33,
                29,
                51,
                88,
                97,
                90,
                82,
                72,
                80,
                74,
                88,
                93,
                78,
                79,
                90,
                82,
                35,
                17,
                13,
                31,
                5,
                5,
                12,
                41,
                35,
                39,
                16,
                30,
                100
            ];
            const rewards = [
                1,
                1,
                0,
                1,
                0,
                1,
                1,
                0,
                1,
                1,
                1,
                0,
                1,
                1,
                0,
                0,
                0,
                1,
                1,
                1,
                1,
                1,
                0,
                1,
                1,
                1,
                1,
                1,
                1,
                1,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                1,
                1,
                1,
                1,
                1,
                0,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                0,
                1,
                0,
                0,
                1,
                1,
                0,
                0,
                0,
                0,
                0,
                0,
                0,
                1,
                1,
                1,
                1,
                0,
                1,
                1,
                0
            ];
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