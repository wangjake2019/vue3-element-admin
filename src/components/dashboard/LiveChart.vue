<!--
 * @Author: your name
 * @Date: 2021-03-16 15:09:41
 * @LastEditTime: 2021-10-15 18:33:00
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \vue3-element-admin\src\components\dashboard\LiveChart.vue
-->
<template>
    <div
        v-resize="myChart && myChart.resize"
        style="height: 400px; margin-top: 20px"
        ref="liveChart"
    ></div>
</template>

<script setup>
import * as echarts from "echarts/core";
import {
    TitleComponent,
    TooltipComponent,
    GridComponent,
} from "echarts/components";
import { BarChart } from "echarts/charts";
import { CanvasRenderer } from "echarts/renderers";
import * as dayjs from "dayjs";
import { unwarp } from "@/utils";
import { onMounted, ref } from "vue";

/**
 * @description: 生成20条数据
 * @param {*}
 * @return {*}
 */
const dataList = () => {
    let date = [];
    let num = [];
    let nowTime = new Date().getTime();
    for (let i = 0; i < 30; i++) {
        date.push(dayjs(nowTime - i * 1000).format("HH:mm:ss"));
        num.push((Math.random() * 10).toFixed(0) * 1);
    }
    return {
        date: date.reverse(),
        num,
    };
};

echarts.use([
    TitleComponent,
    TooltipComponent,
    GridComponent,
    BarChart,
    CanvasRenderer,
]);
const liveChart = ref(null);
const myChart = ref(null);

let $_dataList = dataList();
let option = {
    title: {
        text: "用户访问量",
    },
    grid: {
        left: "0",
        right: "0",
        top: "10%",
        bottom: "0",
        containLabel: true,
    },
    tooltip: {
        trigger: "axis",
    },
    xAxis: {
        data: $_dataList.date,
    },
    yAxis: {
        type: "value",
    },
    series: [
        {
            data: $_dataList.num,
            type: "bar",
            showBackground: true,
            backgroundStyle: {
                color: "rgba(180, 180, 180, 0.2)",
            },
        },
    ],
};
/**
 * @description: 动态数据
 * @param {*}
 * @return {*}
 */
const getNewData = (myChart) => {
    unwarp(myChart.value).setOption(option);

    setInterval(() => {
        $_dataList.date.shift();
        $_dataList.num.shift();
        $_dataList.date.push(dayjs(new Date().getTime()).format("HH:mm:ss"));
        $_dataList.num.push((Math.random() * 10).toFixed(0) * 1);
        unwarp(myChart.value).setOption(option);
    }, 1000);
};
onMounted(() => {
    myChart.value = echarts.init(liveChart.value);
    getNewData(myChart);
});
</script>

<style lang="scss" scoped></style>
