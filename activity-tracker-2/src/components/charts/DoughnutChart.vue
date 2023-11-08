<!--
  this is a child component of the ActivitySummary.vue component

  this chart displays the medium of instruction
-->
<template>
  <Doughnut :data="chartData" :options="chartOptions" />
</template>

<script>
import { ArcElement, Chart as ChartJS, Legend, Tooltip } from 'chart.js';
import { Doughnut } from 'vue-chartjs';

ChartJS.register(ArcElement, Tooltip, Legend);

export default {
  name: 'DoughnutChart',
  components: {
    Doughnut,
  },
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
          title: {
            display: true,
            text: 'Medium of Instruction',
          },
        },
      },
    };
  },
};
</script>
