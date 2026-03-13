<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import * as Cesium from 'cesium'

const mapContainer = ref(null)
let viewer = null

const legendItems = [
  { label: '市政/政府', color: '#00e5a0' },
  { label: '工业园区', color: '#f5a623' },
  { label: '商业中心', color: '#7ec8e3' },
  { label: '交通枢纽', color: '#f5222d' },
  { label: '生态公园', color: '#52c41a' },
  { label: '医疗中心', color: '#ff85c2' },
]

const cityPoints = [
  { name: '市政中心', lon: 116.397, lat: 39.908, type: 'gov' },
  { name: '工业园区', lon: 116.42, lat: 39.93, type: 'industry' },
  { name: '商业中心', lon: 116.38, lat: 39.895, type: 'commerce' },
  { name: '居民区A', lon: 116.36, lat: 39.91, type: 'resident' },
  { name: '交通枢纽', lon: 116.41, lat: 39.88, type: 'traffic' },
  { name: '生态公园', lon: 116.35, lat: 39.93, type: 'park' },
  { name: '医疗中心', lon: 116.405, lat: 39.92, type: 'medical' },
  { name: '教育园区', lon: 116.375, lat: 39.88, type: 'edu' },
]

const typeColors = {
  gov: '#00e5a0',
  industry: '#f5a623',
  commerce: '#7ec8e3',
  resident: '#c0e4ff',
  traffic: '#f5222d',
  park: '#52c41a',
  medical: '#ff85c2',
  edu: '#d4b106',
}

onMounted(() => {
  Cesium.Ion.defaultAccessToken = ''

  viewer = new Cesium.Viewer(mapContainer.value, {
    imageryProvider: new Cesium.TileMapServiceImageryProvider({
      url: Cesium.buildModuleUrl('Assets/Textures/NaturalEarthII'),
      fileExtension: 'jpg',
      maximumLevel: 5,
      credit: new Cesium.Credit('Natural Earth II imagery'),
    }),
    baseLayerPicker: false,
    geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,
    infoBox: false,
    selectionIndicator: false,
    creditContainer: document.createElement('div'),
    terrainProvider: new Cesium.EllipsoidTerrainProvider(),
  })

  viewer.scene.backgroundColor = Cesium.Color.fromCssColorString('#020d1a')
  viewer.scene.globe.baseColor = Cesium.Color.fromCssColorString('#0a2040')
  viewer.scene.skyBox.show = false
  viewer.scene.sun.show = false
  viewer.scene.moon.show = false
  viewer.scene.globe.showGroundAtmosphere = false
  viewer.scene.globe.enableLighting = false

  viewer.camera.flyTo({
    destination: Cesium.Cartesian3.fromDegrees(116.39, 39.91, 80000),
    orientation: {
      heading: Cesium.Math.toRadians(0),
      pitch: Cesium.Math.toRadians(-45),
      roll: 0,
    },
    duration: 2,
  })

  cityPoints.forEach(point => {
    const color = Cesium.Color.fromCssColorString(typeColors[point.type] || '#ffffff')
    viewer.entities.add({
      name: point.name,
      position: Cesium.Cartesian3.fromDegrees(point.lon, point.lat, 0),
      point: {
        pixelSize: 10,
        color,
        outlineColor: Cesium.Color.WHITE.withAlpha(0.5),
        outlineWidth: 1,
        heightReference: Cesium.HeightReference.CLAMP_TO_GROUND,
      },
      label: {
        text: point.name,
        font: '12px Microsoft YaHei',
        fillColor: Cesium.Color.fromCssColorString('#c0e4ff'),
        outlineColor: Cesium.Color.BLACK,
        outlineWidth: 2,
        style: Cesium.LabelStyle.FILL_AND_OUTLINE,
        verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
        pixelOffset: new Cesium.Cartesian2(0, -14),
        heightReference: Cesium.HeightReference.CLAMP_TO_GROUND,
        disableDepthTestDistance: Number.POSITIVE_INFINITY,
      },
    })
  })
})

onUnmounted(() => {
  if (viewer) {
    viewer.destroy()
    viewer = null
  }
})
</script>

<template>
  <div class="cesium-panel">
    <div class="map-overlay-title">
      <span class="dot"></span> 城市实时监控地图
    </div>
    <div ref="mapContainer" class="cesium-container"></div>
    <div class="map-legend">
      <div class="legend-item" v-for="item in legendItems" :key="item.label">
        <span class="legend-dot" :style="{ background: item.color }"></span>
        <span>{{ item.label }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.cesium-panel {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  flex-direction: column;
}

.cesium-container {
  flex: 1;
  width: 100%;
  border: 1px solid rgba(0, 180, 255, 0.3);
  border-radius: 4px;
  overflow: hidden;
}

.map-overlay-title {
  font-size: 12px;
  color: #7dd8ff;
  font-weight: bold;
  letter-spacing: 1px;
  margin-bottom: 6px;
  display: flex;
  align-items: center;
  gap: 6px;
}

.dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #00b4ff;
  box-shadow: 0 0 6px #00b4ff;
}

.map-legend {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
  padding: 6px 0;
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 11px;
  color: #8ab8d8;
}

.legend-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

:deep(.cesium-widget-credits) {
  display: none !important;
}
</style>
