<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref } from "vue";

const props = defineProps(["chart_config", "activeChart", "series"]);
const chartOptions = ref({
	chart: {
		height: 350,
		type: "line",
		dropShadow: {
			enabled: true,
			color: "#000",
			top: 18,
			left: 7,
			blur: 10,
			opacity: 0.9,
		},
		toolbar: {
			show: false,
		},
		grid: {
			borderColor: "#e7e7e7",
			row: {
				colors: ["#f3f3f3", "transparent"],
				opacity: 0.5,
			},
		},
		markers: {
			size: 1,
		},
	},
	colors: ["#2e999b", "#80e3d4"],
	dataLabels: {
		enabled: true,
	},
	stroke: {
		curve: "smooth",
	},
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			const seriesContent = series
				.map((s, index) => {
					const seriesColor = props.chart_config.color[index];
					const seriesDotStyle = `style="display:inline-block; width:10px; height:10px; background-color:${seriesColor}; margin-right:5px; border-radius:50%;"`;
					return `<span ${seriesDotStyle}></span>${w.globals.seriesNames[index]}: ${s[dataPointIndex]} ${props.chart_config.unit}`;
				})
				.join("<br>");
			return (
				'<div class="chart-tooltip">' +
				"<h6>" +
				`${props.chart_config.categories[dataPointIndex]}` +
				"</h6>" +
				"<span>" +
				seriesContent +
				"</span>" +
				"</div>"
			);
		},
	},
	xaxis: {
		axisBorder: {
			show: false,
		},
		axisTicks: {
			show: false,
		},
		categories: props.chart_config.categories
			? props.chart_config.categories
			: [],
		type: "category",
	},

	yaxis: {
		min: 1,
		max: 950,
	},
	legend: {
		position: "top",
		horizontalAlign: "right",
		floating: true,
		offsetY: -25,
		offsetX: -5,
	},

	// To fix a bug when there is more than 1 series
	// Orginal behavior: max will default to the max sum of each series
	max: function (max) {
		if (!props.chart_config.categories) {
			return max;
		}
		let adjustedMax = 0;
		props.series.forEach((element) => {
			const maxOfSeries = Math.max.apply(null, element.data);
			if (maxOfSeries > adjustedMax) {
				adjustedMax = maxOfSeries;
			}
		});
		return adjustedMax * 1.1;
	},
});
</script>

<template>
	<div id="LineCharts">
		<apexchart
			type="line"
			height="350"
			:options="chartOptions"
			:series="series"
			v-tooltip="tooltipOptions"
		></apexchart>
	</div>
</template>
