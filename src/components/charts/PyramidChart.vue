<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref, computed } from 'vue';
import { useMapStore } from '../../store/mapStore';

const props = defineProps(['chart_config', 'activeChart', 'series', 'map_config']);
const mapStore = useMapStore();

const chartOptions = ref({
	chart: 
 {
 	type: 'bar',
 	height: 350,
 	toolbar:{
 		show: false
 	}
 },
	colors: props.chart_config.color,
	dataLabels: 
 {
 	enabled: true,
 	formatter: function (val, opt) 
 	{
 		return opt.w.globals.labels[opt.dataPointIndex] 
 	},
 	dropShadow: 
  {
  	enabled: true,
  },
 },
	grid: {
		show: false,
	},
	legend: {
		show: false,
	},
	plotOptions: 
 {
 	bar: 
  {
  	borderRadius: 0,
  	horizontal: true,
  	distributed: true,
  	barHeight: '80%',
  	isFunnel: true,
  },
 },
	stroke: {
		colors: ['#282a2c'],
		show: true,
		width: 2,
	},
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return '<div class="chart-tooltip">' +
				'<h6>' + w.globals.labels[dataPointIndex] + '</h6>' +
				'<span>' + series[seriesIndex][dataPointIndex] + ` ${props.chart_config.unit}` + '</span>' +
				'</div>';
		},
		followCursor: true,
	},
	xaxis: {
		axisBorder: {
			show: false,
		},
		axisTicks: {
			show: false,
		},
		labels: {
			show: false,
		},
		type: 'category',
	},
});

const sum = computed(() => {
	let sum = 0;
	props.series[0].data.forEach(item => sum += item.y);
	return Math.round(sum * 100) / 100;
});

const selectedIndex = ref(null);

function handleDataSelection(e, chartContext, config) {
	if (!props.chart_config.map_filter) {
		return;
	}
	if (config.dataPointIndex !== selectedIndex.value) {
		mapStore.addLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`, props.chart_config.map_filter[0], props.chart_config.map_filter[1][config.dataPointIndex]);
		selectedIndex.value = config.dataPointIndex;
	} else {
		mapStore.clearLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`);
		selectedIndex.value = null;
	}
}
</script>

<template>
 <div v-if="activeChart === 'PyramidChart'" class="PyramidChart">
  <div class="PyramidChart-title">
   <h5>總合</h5>
   <h6>{{ sum }} {{ chart_config.unit }}</h6>
  </div>
  <apexchart type="bar" height="350" :options="chartOptions" :series="series"
   @dataPointSelection="handleDataSelection"></apexchart>
 </div>
</template>

<style scoped lang="scss">
.PyramidChart {

 &-title {
  display: flex;
  justify-content: center;
  flex-direction: column;
  margin: 0.5rem 0 -0.5rem;

  h5 {
   color: var(--color-complement-text);
  }

  h6 {
   color: var(--color-complement-text);
   font-size: var(--font-m);
   font-weight: 400;
  }
 }
}
</style>