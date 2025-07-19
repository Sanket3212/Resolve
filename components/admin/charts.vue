<template>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    <!-- Pie Chart -->
    <div class="bg-white rounded-xl p-4 shadow">
      <h2 class="text-lg font-semibold mb-2">Bugs by Status</h2>
      <v-chart :option="pieChartOptions" autoresize style="height: 300px" />
    </div>

    <!-- Bar Chart -->
    <div class="bg-white rounded-xl p-4 shadow">
      <h2 class="text-lg font-semibold mb-2">Open Bugs per Project</h2>
      <v-chart :option="barChartOptions" autoresize style="height: 300px" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { use } from 'echarts/core'
import VChart from 'vue-echarts'
import {
  CanvasRenderer
} from 'echarts/renderers'
import {
  PieChart,
  BarChart
} from 'echarts/charts'
import {
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent
} from 'echarts/components'

use([
  CanvasRenderer,
  PieChart,
  BarChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent
])

// Props (optional if data is passed externally)
const pieData = ref([
  { value: 10, name: 'Open' },
  { value: 5, name: 'In Progress' },
  { value: 12, name: 'Resolved' },
  { value: 8, name: 'Closed' }
])

const barData = ref({
  projects: ['Project A', 'Project B', 'Project C'],
  counts: [10, 5, 8]
})

// Chart Options
const pieChartOptions = ref({
  tooltip: {
    trigger: 'item'
  },
  legend: {
    top: 'bottom'
  },
  series: [
    {
      name: 'Bug Status',
      type: 'pie',
      radius: '50%',
      data: pieData.value,
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        }
      }
    }
  ]
})

const barChartOptions = ref({
  tooltip: {
    trigger: 'axis'
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: barData.value.projects
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      name: 'Open Bugs',
      type: 'bar',
      data: barData.value.counts,
      itemStyle: {
        color: '#3B82F6'
      }
    }
  ]
})
</script>

<style scoped>
</style>
