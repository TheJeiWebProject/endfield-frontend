<template>
  <div class="loading-overlay" :class="{ hidden: !show }">
    <!-- 轮播背景图片 -->
    <div class="carousel-bg">
      <div
        v-for="(img, i) in bgImages"
        :key="i"
        class="carousel-slide"
        :class="{ active: i === currentSlide }"
        :style="{ backgroundImage: `url(${img})` }"
      ></div>
    </div>

    <!-- 半透明遮罩 -->
    <div class="bg-overlay"></div>

    <!-- 底部扫描线效果 -->
    <div class="scan-line"></div>

    <!-- 主要内容区 -->
    <div class="loading-center">
      <!-- 旋转六边形 -->
      <div class="hex-spinner">
        <div class="hex-ring hex-ring-1"></div>
        <div class="hex-ring hex-ring-2"></div>
        <div class="hex-ring hex-ring-3"></div>
        <div class="hex-core">
          <div class="core-dot"></div>
        </div>
      </div>

      <!-- 标题 -->
      <div class="loading-title">
        <span class="title-text">{{ i18n.t('app.title') || 'ENDIFIELD SIMULATOR' }}</span>
      </div>

      <!-- 加载文字 -->
      <div class="loading-status">
        <span class="status-dot"></span>
        <span>{{ i18n.t('loading.text') }}</span>
      </div>

      <!-- 资源加载列表 -->
      <div class="resource-list">
        <div
          v-for="res in resources"
          :key="res.name"
          class="resource-item"
          :class="res.status"
        >
          <span class="resource-icon">
            <span v-if="res.status === 'loading'" class="icon-spin">&#9696;</span>
            <span v-else-if="res.status === 'done'" class="icon-done">&#10003;</span>
            <span v-else class="icon-pending">&#9679;</span>
          </span>
          <span class="resource-name">{{ res.displayName }}</span>
          <span class="resource-file">{{ res.name }}</span>
          <span class="resource-bar">
            <span class="resource-fill" :style="{ width: `${res.percent}%` }"></span>
          </span>
        </div>
      </div>

      <!-- 进度条 -->
      <div class="progress-container">
        <div class="progress-track">
          <div class="progress-fill" :style="{ width: `${progress}%` }">
            <div class="progress-glow"></div>
          </div>
          <div class="progress-ticks">
            <span v-for="i in 10" :key="i" class="tick" :style="{ left: `${i * 10}%` }"></span>
          </div>
        </div>
        <span class="progress-percent">{{ progress }}%</span>
      </div>

      <!-- 底部 -->
      <div class="loading-footer">
        <span class="footer-line"></span>
        <span class="footer-text">{{ i18n.t('app.version') || 'v3.0.0' }}</span>
        <span class="footer-line"></span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, onMounted, onUnmounted } from 'vue'
import { useDataStore } from '../stores/data'
import { useI18nStore } from '../stores/i18n'

const dataStore = useDataStore()
const i18n = useI18nStore()

const show = computed(() => dataStore.loading)
const progress = computed(() => dataStore.loadingProgress)
const resources = computed(() => dataStore.loadingResources || [])

// 轮播背景
const bgImages = [
  '/loading_bg/69b2418c4298be0edf042e38_004865de.webp',
  '/loading_bg/69c8b07ec18f320561a63d73_65de0095.webp',
  '/loading_bg/6a223018a9c768179bf2c4cd_65de0095.webp',
  '/loading_bg/6a2ff9a92d317c8420d4364c_0095c241.webp',
  '/loading_bg/6a2ffa62bd3e55ec597f1597_0095c241.webp',
  '/loading_bg/6a2ffb46b17696a8402b6702_e71aef33.webp',
  '/loading_bg/6a2ffcb3c949ebb4dae44809_65de0095.webp',
  '/loading_bg/6a3027b3ef21fc2606aef6ef_65de0095.webp',
  '/loading_bg/6a3028a898c690d8e999bf52_0095c241.webp',
  '/loading_bg/6a302d94e68dd52e2825c0a1_e71aef33.webp',
  '/loading_bg/6a30308fc558a5f603ecd3f7_0095c241.webp',
  '/loading_bg/6a30327e546a394203450b14_65de0095.webp',
  '/loading_bg/6a3e03c5a23ef45ed0bb9aa2_e71aef33.webp'
]

const currentSlide = ref(0)
let carouselTimer = null

onMounted(() => {
  carouselTimer = setInterval(() => {
    currentSlide.value = (currentSlide.value + 1) % bgImages.length
  }, 4000)
})

onUnmounted(() => {
  if (carouselTimer) clearInterval(carouselTimer)
})
</script>

<style scoped>
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #040810;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  transition: opacity 0.6s ease, visibility 0.6s ease;
}
.loading-overlay.hidden {
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
}

/* ===== 轮播背景 ===== */
.carousel-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
}
.carousel-slide {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  opacity: 0;
  transition: opacity 1.2s cubic-bezier(0.4, 0, 0.2, 1);
  transform: scale(1.05);
  transition-property: opacity, transform;
}
.carousel-slide.active {
  opacity: 1;
  transform: scale(1);
}

/* 遮罩层 */
.bg-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background:
    radial-gradient(ellipse at center, rgba(4, 8, 16, 0.3) 0%, rgba(4, 8, 16, 0.85) 100%);
  z-index: 1;
}

/* 扫描线 */
.scan-line {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2;
  pointer-events: none;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 0, 0, 0.03) 2px,
    rgba(0, 0, 0, 0.03) 4px
  );
}

/* ===== 中心内容 ===== */
.loading-center {
  position: relative;
  z-index: 3;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

/* 六边形旋转器 */
.hex-spinner {
  position: relative;
  width: 90px;
  height: 90px;
  margin-bottom: 0.5rem;
}
.hex-ring {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 2px solid transparent;
  clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%);
}
.hex-ring-1 {
  border-color: rgba(0, 212, 255, 0.2);
  animation: hexSpin 3s linear infinite;
}
.hex-ring-2 {
  width: 75%; height: 75%;
  top: 12.5%; left: 12.5%;
  border-color: rgba(0, 212, 255, 0.35);
  animation: hexSpin 2s linear infinite reverse;
}
.hex-ring-3 {
  width: 50%; height: 50%;
  top: 25%; left: 25%;
  border-color: rgba(0, 212, 255, 0.55);
  animation: hexSpin 1.5s linear infinite;
}
.hex-core {
  position: absolute;
  top: 50%; left: 50%;
  width: 12px; height: 12px;
  transform: translate(-50%, -50%);
}
.core-dot {
  width: 100%; height: 100%;
  background: #00D4FF;
  border-radius: 50%;
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.9), 0 0 40px rgba(0, 212, 255, 0.5);
  animation: corePulse 1.5s ease-in-out infinite;
}

@keyframes hexSpin {
  to { transform: rotate(360deg); }
}
@keyframes corePulse {
  0%, 100% { transform: scale(1); opacity: 0.6; }
  50% { transform: scale(1.8); opacity: 1; }
}

/* 标题 */
.loading-title { text-align: center; }
.title-text {
  font-family: 'Orbitron', 'Rajdhani', sans-serif;
  font-size: 1.1rem;
  font-weight: 600;
  letter-spacing: 0.35em;
  color: #00D4FF;
  text-shadow: 0 0 24px rgba(0, 212, 255, 0.6), 0 0 4px rgba(0, 0, 0, 0.8);
  text-transform: uppercase;
}

/* 加载状态 */
.loading-status {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: #E8EEF4;
  font-size: 1.05rem;
  font-weight: 500;
  text-shadow: 0 0 4px rgba(0, 0, 0, 0.7);
}
.status-dot {
  width: 6px; height: 6px;
  background: #00D4FF;
  border-radius: 50%;
  animation: statusBlink 0.8s ease-in-out infinite;
}
@keyframes statusBlink {
  0%, 100% { opacity: 0.3; }
  50% { opacity: 1; }
}

/* 资源加载列表 */
.resource-list {
  display: flex;
  flex-direction: column;
  gap: 5px;
  min-width: 320px;
  max-width: 420px;
}

.resource-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 10px;
  background: rgba(4, 8, 16, 0.6);
  backdrop-filter: blur(6px);
  border: 1px solid rgba(0, 212, 255, 0.1);
  border-radius: 4px;
  font-size: 12px;
  transition: border-color 0.3s;
}
.resource-item.loading {
  border-color: rgba(0, 212, 255, 0.25);
}
.resource-item.done {
  border-color: rgba(0, 255, 136, 0.2);
}

.resource-icon {
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  flex-shrink: 0;
}
.icon-pending { color: #4A5E73; font-size: 6px; }
.icon-spin { color: #00D4FF; animation: spin 1s linear infinite; }
.icon-done { color: #00FF88; font-size: 12px; font-weight: bold; }

@keyframes spin {
  to { transform: rotate(360deg); }
}

.resource-name {
  color: #E8EEF4;
  min-width: 60px;
  flex-shrink: 0;
}
.resource-item.done .resource-name {
  color: #7A8FA6;
}

.resource-file {
  flex: 1;
  color: #4A5E73;
  font-family: 'Consolas', monospace;
  font-size: 10px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.resource-bar {
  width: 60px;
  height: 3px;
  background: rgba(0, 212, 255, 0.1);
  border-radius: 3px;
  overflow: hidden;
  flex-shrink: 0;
}
.resource-fill {
  display: block;
  height: 100%;
  background: #00D4FF;
  border-radius: 3px;
  transition: width 0.4s ease-out;
}
.resource-item.done .resource-fill {
  background: #00FF88;
}

/* 进度条 */
.progress-container {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}
.progress-track {
  position: relative;
  width: 280px;
  height: 5px;
  background: rgba(4, 8, 16, 0.7);
  border-radius: 10px;
  overflow: hidden;
  border: 1px solid rgba(0, 212, 255, 0.15);
  backdrop-filter: blur(4px);
}
.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #006688, #00D4FF, #006688);
  background-size: 300% 100%;
  border-radius: 10px;
  transition: width 0.4s ease-out;
  position: relative;
  animation: progressFlow 2s linear infinite;
}
.progress-glow {
  position: absolute;
  right: 0; top: -2px;
  height: 9px; width: 20px;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.5));
  filter: blur(2px);
}
.progress-ticks {
  position: absolute;
  top: 0; left: 0;
  width: 100%; height: 100%;
}
.tick {
  position: absolute;
  top: 0;
  width: 1px;
  height: 100%;
  background: rgba(0, 212, 255, 0.2);
  transform: translateX(-50%);
}
.progress-percent {
  color: #00D4FF;
  font-family: 'Consolas', monospace;
  font-size: 0.8rem;
  font-weight: 500;
  min-width: 36px;
  text-align: right;
  text-shadow: 0 0 4px rgba(0, 0, 0, 0.7);
}

@keyframes progressFlow {
  to { background-position: 300% center; }
}

/* 底部 */
.loading-footer {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-top: 0.25rem;
}
.footer-line {
  width: 40px; height: 1px;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.25), transparent);
}
.footer-text {
  color: #4A5E73;
  font-family: 'Orbitron', sans-serif;
  font-size: 0.6rem;
  letter-spacing: 0.15em;
  text-shadow: 0 0 3px rgba(0, 0, 0, 0.6);
}

/* 响应式 */
@media (max-width: 480px) {
  .hex-spinner { width: 70px; height: 70px; }
  .title-text { font-size: 0.9rem; }
  .loading-status { font-size: 0.95rem; }
  .progress-track { width: 200px; }
}
</style>
