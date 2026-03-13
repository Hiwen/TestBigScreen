<script setup>
import { ref, onMounted, onUnmounted, nextTick } from 'vue'
import * as echarts from 'echarts'

const chartRef = ref(null)
const lineChartRef = ref(null)
let barChart = null
let lineChart = null
let timer = null

const metrics = ref([
  { label: '在线设备', value: 1284, unit: '台', color: '#00e5a0', icon: '🖥️' },
  { label: '今日告警', value: 47, unit: '次', color: '#f5a623', icon: '⚠️' },
  { label: '网络流量', value: '2.4', unit: 'GB/s', color: '#7ec8e3', icon: '📡' },
  { label: '能耗监控', value: '98.2', unit: 'kWh', color: '#ff85c2', icon: '⚡' },
])

const sysStatus = ref([
  { name: 'CPU使用率', value: 42, color: '#00b4ff' },
  { name: '内存占用', value: 67, color: '#f5a623' },
  { name: '存储空间', value: 55, color: '#00e5a0' },
  { name: '网络带宽', value: 78, color: '#ff85c2' },
])

const categories = ['交通', '环境', '能源', '安防', '照明', '消防']
const barData = ref([320, 180, 240, 410, 290, 150])

function getLineData() {
  return Array.from({ length: 12 }, () => Math.floor(Math.random() * 400 + 100))
}

function initBarChart() {
  barChart = echarts.init(chartRef.value)
  barChart.setOption({
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'axis',
      axisPointer: { type: 'shadow' },
      backgroundColor: 'rgba(0,20,50,0.9)',
      borderColor: 'rgba(0,180,255,0.4)',
      textStyle: { color: '#c0e4ff', fontSize: 11 },
    },
    grid: { left: 8, right: 8, top: 10, bottom: 20, containLabel: true },
    xAxis: {
      type: 'category',
      data: categories,
      axisLabel: { color: '#7aa8c8', fontSize: 10 },
      axisLine: { lineStyle: { color: 'rgba(0,180,255,0.2)' } },
      axisTick: { show: false },
    },
    yAxis: {
      type: 'value',
      axisLabel: { color: '#7aa8c8', fontSize: 10 },
      splitLine: { lineStyle: { color: 'rgba(0,180,255,0.1)' } },
      axisLine: { show: false },
    },
    series: [{
      type: 'bar',
      data: barData.value,
      barWidth: '60%',
      itemStyle: {
        color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
          { offset: 0, color: '#00b4ff' },
          { offset: 1, color: 'rgba(0,180,255,0.1)' },
        ]),
        borderRadius: [3, 3, 0, 0],
      },
    }],
  })
}

function initLineChart() {
  lineChart = echarts.init(lineChartRef.value)
  lineChart.setOption({
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(0,20,50,0.9)',
      borderColor: 'rgba(0,180,255,0.4)',
      textStyle: { color: '#c0e4ff', fontSize: 11 },
    },
    grid: { left: 8, right: 8, top: 10, bottom: 20, containLabel: true },
    xAxis: {
      type: 'category',
      data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
      axisLabel: { color: '#7aa8c8', fontSize: 9 },
      axisLine: { lineStyle: { color: 'rgba(0,180,255,0.2)' } },
      axisTick: { show: false },
    },
    yAxis: {
      type: 'value',
      axisLabel: { color: '#7aa8c8', fontSize: 10 },
      splitLine: { lineStyle: { color: 'rgba(0,180,255,0.1)' } },
      axisLine: { show: false },
    },
    series: [{
      type: 'line',
      data: getLineData(),
      smooth: true,
      symbol: 'circle',
      symbolSize: 4,
      lineStyle: { color: '#00e5a0', width: 2 },
      itemStyle: { color: '#00e5a0' },
      areaStyle: {
        color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
          { offset: 0, color: 'rgba(0,229,160,0.3)' },
          { offset: 1, color: 'rgba(0,229,160,0.0)' },
        ]),
      },
    }],
  })
}

function updateData() {
  barData.value = barData.value.map(v => Math.max(50, v + Math.floor(Math.random() * 60 - 30)))
  barChart && barChart.setOption({ series: [{ data: barData.value }] })
  lineChart && lineChart.setOption({ series: [{ data: getLineData() }] })
  metrics.value[0].value = Math.floor(1200 + Math.random() * 200)
  metrics.value[1].value = Math.floor(30 + Math.random() * 40)
  sysStatus.value = sysStatus.value.map(s => ({
    ...s,
    value: Math.min(95, Math.max(20, s.value + Math.floor(Math.random() * 10 - 5))),
  }))
}

onMounted(async () => {
  await nextTick()
  initBarChart()
  initLineChart()
  timer = setInterval(updateData, 3000)
})

onUnmounted(() => {
  clearInterval(timer)
  barChart && barChart.dispose()
  lineChart && lineChart.dispose()
})
</script>

<template>
  <div class="right-panel">
    <div class="panel-section">
      <div class="section-title">
        <span class="title-dot"></span>实时数据监控
      </div>
      <div class="metrics-grid">
        <div class="metric-card" v-for="m in metrics" :key="m.label">
          <div class="metric-icon">{{ m.icon }}</div>
          <div class="metric-content">
            <div class="metric-value" :style="{ color: m.color }">
              {{ m.value }}<span class="metric-unit">{{ m.unit }}</span>
            </div>
            <div class="metric-label">{{ m.label }}</div>
          </div>
        </div>
      </div>
    </div>

    <div class="panel-section chart-section">
      <div class="section-title">
        <span class="title-dot"></span>各类设备报警统计
      </div>
      <div ref="chartRef" class="chart"></div>
    </div>

    <div class="panel-section chart-section">
      <div class="section-title">
        <span class="title-dot green"></span>年度事件趋势
      </div>
      <div ref="lineChartRef" class="chart"></div>
    </div>

    <div class="panel-section">
      <div class="section-title">
        <span class="title-dot orange"></span>系统运行状态
      </div>
      <div class="sys-status">
        <div class="sys-item" v-for="sys in sysStatus" :key="sys.name">
          <div class="sys-name">{{ sys.name }}</div>
          <div class="sys-bar-wrap">
            <div class="sys-bar" :style="{ width: sys.value + '%', background: sys.color }"></div>
          </div>
          <div class="sys-val" :style="{ color: sys.color }">{{ sys.value }}%</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.right-panel {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 12px;
  overflow: hidden;
}

.panel-section {
  background: rgba(0, 30, 60, 0.7);
  border: 1px solid rgba(0, 180, 255, 0.2);
  border-radius: 4px;
  padding: 12px;
}

.chart-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;
}

.section-title {
  font-size: 13px;
  font-weight: bold;
  color: #7dd8ff;
  letter-spacing: 1px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 6px;
}

.title-dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #00b4ff;
  box-shadow: 0 0 6px #00b4ff;
}

.title-dot.green {
  background: #00e5a0;
  box-shadow: 0 0 6px #00e5a0;
}

.title-dot.orange {
  background: #f5a623;
  box-shadow: 0 0 6px #f5a623;
}

.metrics-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
}

.metric-card {
  background: rgba(0, 40, 80, 0.6);
  border-radius: 4px;
  padding: 8px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.metric-icon {
  font-size: 18px;
}

.metric-value {
  font-size: 18px;
  font-weight: bold;
  line-height: 1.2;
}

.metric-unit {
  font-size: 10px;
  font-weight: normal;
  margin-left: 2px;
  color: #8ab8d8;
}

.metric-label {
  font-size: 11px;
  color: #8ab8d8;
}

.chart {
  flex: 1;
  min-height: 100px;
}

.sys-status {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.sys-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 11px;
}

.sys-name {
  width: 60px;
  color: #8ab8d8;
  flex-shrink: 0;
}

.sys-bar-wrap {
  flex: 1;
  height: 6px;
  background: rgba(0, 40, 80, 0.8);
  border-radius: 3px;
  overflow: hidden;
}

.sys-bar {
  height: 100%;
  border-radius: 3px;
  transition: width 0.5s ease;
}

.sys-val {
  width: 36px;
  text-align: right;
  font-weight: bold;
  font-size: 12px;
}
</style>
