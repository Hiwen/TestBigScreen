<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import LeftPanel from './components/LeftPanel.vue'
import CesiumMap from './components/CesiumMap.vue'
import RightPanel from './components/RightPanel.vue'

const currentTime = ref('')
const navItems = ['首页', '设备管理', '告警中心', '数据分析', '系统设置']
const activeNav = ref('首页')

let clockTimer = null

function updateTime() {
  const now = new Date()
  currentTime.value = now.toLocaleString('zh-CN', {
    year: 'numeric', month: '2-digit', day: '2-digit',
    hour: '2-digit', minute: '2-digit', second: '2-digit',
    hour12: false,
  })
}

onMounted(() => {
  updateTime()
  clockTimer = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(clockTimer)
})
</script>

<template>
  <div class="big-screen">
    <!-- Header -->
    <header class="header">
      <div class="header-left">
        <span class="header-logo">🌐</span>
        <span class="header-sub">Smart City</span>
      </div>
      <div class="header-center">
        <div class="header-title-wrap">
          <div class="header-title-deco left"></div>
          <h1 class="header-title">智慧城市运行监控大屏</h1>
          <div class="header-title-deco right"></div>
        </div>
        <nav class="nav-bar">
          <span
            v-for="item in navItems"
            :key="item"
            class="nav-item"
            :class="{ active: activeNav === item }"
            @click="activeNav = item"
          >{{ item }}</span>
        </nav>
      </div>
      <div class="header-right">
        <div class="clock">{{ currentTime }}</div>
        <div class="weather">⛅ 北京 18°C 晴</div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="main-content">
      <aside class="left-col">
        <LeftPanel />
      </aside>
      <section class="center-col">
        <CesiumMap />
      </section>
      <aside class="right-col">
        <RightPanel />
      </aside>
    </main>

    <!-- Footer -->
    <footer class="footer">
      © 2024 智慧城市运行监控平台 &nbsp;|&nbsp; 技术支持：Vue3 + Cesium + ECharts &nbsp;|&nbsp; 版本 v1.0.0
    </footer>
  </div>
</template>

<style scoped>
.big-screen {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  background: radial-gradient(ellipse at center, #061525 0%, #020d1a 100%);
  position: relative;
  overflow: hidden;
}

/* HEADER */
.header {
  height: 80px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  padding: 0 20px;
  background: linear-gradient(180deg, rgba(0, 30, 60, 0.95) 0%, rgba(0, 20, 45, 0.8) 100%);
  border-bottom: 1px solid rgba(0, 180, 255, 0.3);
  box-shadow: 0 2px 20px rgba(0, 180, 255, 0.15);
  position: relative;
  z-index: 10;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 8px;
  min-width: 160px;
}

.header-logo {
  font-size: 28px;
  filter: drop-shadow(0 0 8px #00b4ff);
}

.header-sub {
  font-size: 13px;
  color: #4a9fc8;
  letter-spacing: 2px;
  text-transform: uppercase;
}

.header-center {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
}

.header-title-wrap {
  display: flex;
  align-items: center;
  gap: 16px;
}

.header-title-deco {
  width: 60px;
  height: 2px;
  background: linear-gradient(90deg, transparent, #00b4ff);
}

.header-title-deco.right {
  background: linear-gradient(90deg, #00b4ff, transparent);
}

.header-title {
  font-size: 24px;
  font-weight: bold;
  letter-spacing: 4px;
  color: #ffffff;
  text-shadow: 0 0 20px rgba(0, 180, 255, 0.8), 0 0 40px rgba(0, 180, 255, 0.4);
  white-space: nowrap;
}

.nav-bar {
  display: flex;
  gap: 4px;
}

.nav-item {
  padding: 3px 14px;
  font-size: 12px;
  color: #7aa8c8;
  cursor: pointer;
  border-radius: 12px;
  transition: all 0.2s;
  letter-spacing: 1px;
}

.nav-item:hover {
  color: #c0e4ff;
  background: rgba(0, 180, 255, 0.1);
}

.nav-item.active {
  color: #00b4ff;
  background: rgba(0, 180, 255, 0.15);
  border: 1px solid rgba(0, 180, 255, 0.4);
}

.header-right {
  min-width: 160px;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 4px;
}

.clock {
  font-size: 15px;
  font-weight: bold;
  color: #00e5a0;
  font-family: monospace;
  letter-spacing: 1px;
}

.weather {
  font-size: 12px;
  color: #7aa8c8;
}

/* MAIN CONTENT */
.main-content {
  flex: 1;
  display: flex;
  gap: 12px;
  padding: 12px 16px;
  overflow: hidden;
  min-height: 0;
}

.left-col {
  width: 280px;
  flex-shrink: 0;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.center-col {
  flex: 1;
  min-width: 0;
  overflow: hidden;
}

.right-col {
  width: 300px;
  flex-shrink: 0;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

/* FOOTER */
.footer {
  height: 32px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 11px;
  color: #3a6080;
  background: rgba(0, 15, 35, 0.8);
  border-top: 1px solid rgba(0, 180, 255, 0.15);
  letter-spacing: 1px;
}
</style>
