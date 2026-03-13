<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const statusMap = {
  normal: { label: '正常', color: '#00e5a0' },
  warning: { label: '报警', color: '#f5a623' },
  fault: { label: '故障', color: '#f5222d' },
  offline: { label: '离线', color: '#8c8c8c' },
}

const devices = ref([
  { id: 'D001', name: '交通信号控制器-01', type: '交通设备', status: 'normal', location: '中心广场路口' },
  { id: 'D002', name: '环境监测站-02', type: '环境设备', status: 'warning', location: '工业园区北侧' },
  { id: 'D003', name: '智能路灯-03', type: '照明设备', status: 'normal', location: '滨河大道' },
  { id: 'D004', name: '视频监控-04', type: '安防设备', status: 'fault', location: '商业中心' },
  { id: 'D005', name: '智能停车-05', type: '停车设备', status: 'normal', location: '市政广场' },
  { id: 'D006', name: '噪声检测仪-06', type: '环境设备', status: 'offline', location: '居民区东路' },
  { id: 'D007', name: '气象站-07', type: '气象设备', status: 'normal', location: '城市中心' },
  { id: 'D008', name: '消防感知器-08', type: '消防设备', status: 'warning', location: '商业大厦B栋' },
])

const alarms = ref([
  { id: 'A001', time: '11:42:31', device: '环境监测站-02', msg: 'PM2.5超标报警', level: 'warning' },
  { id: 'A002', time: '11:38:05', device: '视频监控-04', msg: '设备离线异常', level: 'fault' },
  { id: 'A003', time: '11:25:18', device: '消防感知器-08', msg: '烟雾浓度升高', level: 'warning' },
  { id: 'A004', time: '10:55:44', device: '噪声检测仪-06', msg: '通信链路中断', level: 'fault' },
])

const stats = ref({ normal: 0, warning: 0, fault: 0, offline: 0 })

function calcStats() {
  stats.value = { normal: 0, warning: 0, fault: 0, offline: 0 }
  devices.value.forEach(d => { stats.value[d.status]++ })
}

let timer = null

function randomUpdate() {
  const statuses = ['normal', 'warning', 'fault', 'offline']
  const idx = Math.floor(Math.random() * devices.value.length)
  const newStatus = statuses[Math.floor(Math.random() * statuses.length)]
  devices.value[idx] = { ...devices.value[idx], status: newStatus }
  calcStats()
}

onMounted(() => {
  calcStats()
  timer = setInterval(randomUpdate, 4000)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>

<template>
  <div class="left-panel">
    <div class="panel-section">
      <div class="section-title">
        <span class="title-dot"></span>设备状态总览
      </div>
      <div class="stat-grid">
        <div class="stat-card normal">
          <span class="stat-num">{{ stats.normal }}</span>
          <span class="stat-label">正常</span>
        </div>
        <div class="stat-card warning">
          <span class="stat-num">{{ stats.warning }}</span>
          <span class="stat-label">报警</span>
        </div>
        <div class="stat-card fault">
          <span class="stat-num">{{ stats.fault }}</span>
          <span class="stat-label">故障</span>
        </div>
        <div class="stat-card offline">
          <span class="stat-num">{{ stats.offline }}</span>
          <span class="stat-label">离线</span>
        </div>
      </div>
    </div>

    <div class="panel-section device-list-section">
      <div class="section-title">
        <span class="title-dot"></span>设备列表
      </div>
      <div class="device-list">
        <div
          v-for="device in devices"
          :key="device.id"
          class="device-item"
          :class="device.status"
        >
          <div class="device-header">
            <span class="device-name">{{ device.name }}</span>
            <span class="device-status-badge" :style="{ color: statusMap[device.status].color, borderColor: statusMap[device.status].color }">
              {{ statusMap[device.status].label }}
            </span>
          </div>
          <div class="device-info">
            <span class="device-type">{{ device.type }}</span>
            <span class="device-location">📍 {{ device.location }}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="panel-section alarm-section">
      <div class="section-title">
        <span class="title-dot red"></span>报警信息
        <span class="alarm-count">{{ alarms.length }}</span>
      </div>
      <div class="alarm-list">
        <div
          v-for="alarm in alarms"
          :key="alarm.id"
          class="alarm-item"
          :class="alarm.level"
        >
          <div class="alarm-time">{{ alarm.time }}</div>
          <div class="alarm-device">{{ alarm.device }}</div>
          <div class="alarm-msg">{{ alarm.msg }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.left-panel {
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

.device-list-section {
  flex: 1;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.alarm-section {
  max-height: 200px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
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

.title-dot.red {
  background: #f5222d;
  box-shadow: 0 0 6px #f5222d;
}

.alarm-count {
  margin-left: auto;
  background: #f5222d;
  color: #fff;
  font-size: 11px;
  padding: 0 6px;
  border-radius: 10px;
}

.stat-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
}

.stat-card {
  background: rgba(0, 40, 80, 0.6);
  border-radius: 4px;
  padding: 8px;
  text-align: center;
  border-left: 3px solid;
}

.stat-card.normal { border-color: #00e5a0; }
.stat-card.warning { border-color: #f5a623; }
.stat-card.fault { border-color: #f5222d; }
.stat-card.offline { border-color: #8c8c8c; }

.stat-num {
  display: block;
  font-size: 24px;
  font-weight: bold;
  line-height: 1;
}

.stat-card.normal .stat-num { color: #00e5a0; }
.stat-card.warning .stat-num { color: #f5a623; }
.stat-card.fault .stat-num { color: #f5222d; }
.stat-card.offline .stat-num { color: #8c8c8c; }

.stat-label {
  font-size: 11px;
  color: #8ab8d8;
  margin-top: 2px;
}

.device-list {
  flex: 1;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(0,180,255,0.3) transparent;
}

.device-item {
  padding: 8px;
  margin-bottom: 6px;
  background: rgba(0, 40, 80, 0.5);
  border-radius: 3px;
  border-left: 3px solid transparent;
  transition: border-color 0.3s;
}

.device-item.normal { border-left-color: #00e5a0; }
.device-item.warning { border-left-color: #f5a623; }
.device-item.fault { border-left-color: #f5222d; }
.device-item.offline { border-left-color: #8c8c8c; }

.device-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.device-name {
  font-size: 12px;
  color: #c0e4ff;
  font-weight: 500;
}

.device-status-badge {
  font-size: 10px;
  padding: 1px 6px;
  border: 1px solid;
  border-radius: 10px;
}

.device-info {
  display: flex;
  gap: 8px;
  font-size: 11px;
  color: #6a90b0;
}

.alarm-list {
  overflow-y: auto;
  flex: 1;
  scrollbar-width: thin;
  scrollbar-color: rgba(255,50,50,0.3) transparent;
}

.alarm-item {
  padding: 7px 8px;
  margin-bottom: 5px;
  border-radius: 3px;
  border-left: 3px solid;
  font-size: 11px;
  line-height: 1.6;
}

.alarm-item.warning {
  background: rgba(245, 166, 35, 0.08);
  border-left-color: #f5a623;
}

.alarm-item.fault {
  background: rgba(245, 34, 45, 0.08);
  border-left-color: #f5222d;
}

.alarm-time {
  color: #8ab8d8;
  font-size: 10px;
}

.alarm-device {
  color: #c0e4ff;
  font-weight: 500;
}

.alarm-msg {
  color: #e8c070;
}
</style>
