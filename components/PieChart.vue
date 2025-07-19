<template>
  <Pie :data="chartData" :options="chartOptions" />
</template>

<script setup>
import { Pie } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement, CategoryScale } from 'chart.js'

// Register the necessary components for Chart.js
ChartJS.register(Title, Tooltip, Legend, ArcElement, CategoryScale)

// Define the component's props
defineProps({
  chartData: {
    type: Object,
    required: true
  }
})

// Configuration options for the chart
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'bottom',
      labels: {
        color: '#d4d4d8', // zinc-300
        padding: 20,
      }
    }
  }
}
</script>