<!-- this is a child component of the ActivitySummary.vue component -->
<template>
  <Bar id="activity-n-hours-chart" :data="chartData" :options="chartOptions" />
</template>

<script>
import {
  BarElement,
  CategoryScale,
  Chart as ChartJS,
  Legend,
  LinearScale,
  Title,
  Tooltip,
} from 'chart.js';
import { Bar } from 'vue-chartjs';

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

export default {
  name: 'BarChart',
  components: { Bar },
  props: {
    chartData: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      chartOptions: {
        responsive: true,
        plugins: {
          // legend: {
          //   title: {
          //     display: true,
          //     text: 'Hours practiced',
          //   },
          // },
          title: {
            display: true,
            text: 'Activity & Hours Practiced',
          },
        },
      },
    };
  },
};
</script>
