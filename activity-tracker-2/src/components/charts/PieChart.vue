<!--
  this is a child component of the ActivitySummary.vue component

  this chart displays the status of the activity, completed or pending
-->
<template>
  <Pie :data="chartData" :options="chartOptions" />
</template>

<script>
import { ArcElement, Chart as ChartJS, Legend, Tooltip } from 'chart.js';
import { Pie } from 'vue-chartjs';

ChartJS.register(ArcElement, Tooltip, Legend);

export default {
  name: 'PieChart',
  components: {
    Pie,
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
            text: 'Status: Completed/Pending',
          },
        },
      },
    };
  },
};
</script>
