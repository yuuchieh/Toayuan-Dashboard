<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref } from "vue";

const props = defineProps(["chart_config", "activeChart", "series"]);

const chartOptions = ref({
	chart: {
		stacked: true,
		height: 350,
		type: "rangeArea",
		animations: {
			speed: 500,
		},
		toolbar: {
			show: false,
		},
	},
	dataLabels: {
		enabled: false,
	},
	fill: {
		opacity: [0.24, 0.24, 1, 1],
	},
	forecastDataPoints: {
		count: 2,
	},
	stroke: {
		curve: "straight",
		width: [0, 0, 2, 2],
	},
	legend: {
		show: true,
		inverseOrder: true,
	},
});
</script>

<template>
	<div v-if="activeChart === 'RangeAreaChart'">
		<apexchart
			width="100%"
			height="260px"
			type="rangeArea"
			:options="chartOptions"
			:series="series"
		></apexchart>
	</div>
</template>
