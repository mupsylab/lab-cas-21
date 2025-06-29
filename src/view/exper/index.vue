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
    timeline: [{
        component: h(HtmlKeyboard, {
            stimulus: "+",
            choices: ["NO_KEYS"],
            trial_duration_time: 500,
        })
    }, {
        component: function() {
            const {location = 25, reward = true, showCloud = false} = jspsych.currTrial.parent.getAllTimelineVariables()
            return h(CoreScreen, {
                location: location,
                reward: reward,
                showCloud: showCloud
            })
        }
    }],
    timeline_variables: [
        { location: 25, reward: true, showCloud: false },
        { location: 50, reward: true, showCloud: false },
        { location: 75, reward: true, showCloud: false },
        { location: 25, reward: false, showCloud: true },
        { location: 50, reward: false, showCloud: true },
        { location: 75, reward: false, showCloud: true }
    ],
})

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