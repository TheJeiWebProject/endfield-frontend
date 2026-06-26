<template>
  <div id="app">
    <div class="app-bg">
      <div class="bg-gradient" />
    </div>
    
    <div class="app-content">
      <LoadingOverlay v-if="dataStore.loading" />
      <WelcomeBanner />
      <AppHeader />
      
      <main class="main-container" :class="{ 'canvas-mode': $route.path === '/canvas' }">
        <div class="main-content">
          <router-view />
        </div>
        
        <aside v-if="showSidebar" class="main-sidebar">
          <div class="sidebar-card">
            <h3>{{ i18n.t('sidebar.title') }}</h3>
            <div class="sidebar-links">
              <a v-for="link in sidebarLinks" :key="link.path" :href="link.path" class="sidebar-link">
                {{ link.icon }} {{ i18n.t(link.key) }}
              </a>
            </div>
          </div>
        </aside>
      </main>
      
      <footer v-if="$route.path !== '/canvas'" class="app-footer">
        <div class="footer-content">
          <div class="footer-left">
            <span>{{ i18n.t('app.title') }}</span>
            <span class="footer-version">{{ i18n.t('app.version') }}</span>
          </div>
          <div class="footer-right">
            <span>{{ i18n.t('footer.copyright') }}</span>
          </div>
        </div>
      </footer>
      
      <TutorialModal ref="tutorialModal" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, provide } from 'vue'
import { useDataStore } from './stores/data'
import { useI18nStore } from './stores/i18n'
import { generateBackground } from './utils/backgroundGenerator'
import LoadingOverlay from './components/LoadingOverlay.vue'
import WelcomeBanner from './components/WelcomeBanner.vue'
import AppHeader from './components/AppHeader.vue'
import TutorialModal from './components/TutorialModal.vue'

const dataStore = useDataStore()
const i18n = useI18nStore()
const tutorialModal = ref(null)
const showSidebar = ref(false)

function restartTutorial() {
  if (tutorialModal.value) {
    tutorialModal.value.reset()
    tutorialModal.value.show()
  }
}

function openTutorial() {
  if (tutorialModal.value) {
    tutorialModal.value.show()
  }
}

provide('restartTutorial', restartTutorial)
provide('openTutorial', openTutorial)

// Expose to window for external access
window.restartTutorial = restartTutorial

const sidebarLinks = [
  { path: '/guide', key: 'nav.guide', icon: '📖' },
  { path: '/items', key: 'nav.items', icon: '📦' },
  { path: '/recipes', key: 'nav.recipes', icon: '🔬' },
  { path: '/calculator', key: 'nav.calculator', icon: '🧮' }
]

const getParticleStyle = (n) => {
  const left = Math.random() * 100
  const top = Math.random() * 100
  const delay = Math.random() * 5
  const duration = 10 + Math.random() * 10
  const size = 2 + Math.random() * 4
  
  return {
    left: `${left}%`,
    top: `${top}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${duration}s`,
    width: `${size}px`,
    height: `${size}px`
  }
}

onMounted(() => {
  dataStore.loadData()
  generateBackgroundImage()
  window.addEventListener('resize', generateBackgroundImage)
})

function generateBackgroundImage() {
  const canvas = document.getElementById('bgCanvas')
  if (canvas) {
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
    generateBackground('bgCanvas')
  }
}
</script>

<style>
#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow-x: hidden;
}

.app-bg {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  pointer-events: none;
}

.bg-gradient {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #000000;
}

.app-content {
  position: relative;
  z-index: 1;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.main-container {
  flex: 1;
  display: flex;
  gap: 2rem;
  padding: 2rem;
  max-width: 1600px;
  margin: 0 auto;
  width: 100%;
}

.main-container.canvas-mode {
  padding: 0;
  max-width: none;
  margin: 0;
  gap: 0;
}

.main-content {
  flex: 1;
  animation: fadeInPage 0.6s ease-out;
}

@keyframes fadeInPage {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.main-sidebar {
  width: 280px;
  flex-shrink: 0;
}

.sidebar-card {
  background: #0a0a0a;
  border: 1px solid rgba(212, 175, 55, 0.3);
  border-radius: 20px;
  padding: 1.5rem;
  position: relative;
}

.sidebar-card::before,
.sidebar-card::after {
  content: '';
  position: absolute;
  width: 20px;
  height: 20px;
  border-color: #d4af37;
  border-style: solid;
  opacity: 0.5;
}

.sidebar-card::before {
  top: 10px;
  left: 10px;
  border-width: 2px 0 0 2px;
}

.sidebar-card::after {
  bottom: 10px;
  right: 10px;
  border-width: 0 2px 2px 0;
}

.sidebar-card h3 {
  color: #d4af37;
  font-size: 1.1rem;
  margin-bottom: 1rem;
  padding-bottom: 0.75rem;
  border-bottom: 1px solid rgba(255, 107, 122, 0.2);
}

.sidebar-links {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.sidebar-link {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.75rem 1rem;
  color: #f0f0f0;
  text-decoration: none;
  border-radius: 10px;
  transition: all 0.3s;
  background: rgba(30, 30, 50, 0.6);
}

.sidebar-link:hover {
  background: rgba(212, 175, 55, 0.15);
  transform: translateX(5px);
  color: #d4af37;
}

.app-footer {
  background: #0A1020;
  border-top: 1px solid rgba(0, 212, 255, 0.12);
  padding: 0.8rem 2rem;
  margin-top: auto;
}

.footer-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1400px;
  margin: 0 auto;
}

.footer-left {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  color: #7A8FA6;
  font-size: 0.82rem;
}

.footer-icon {
  font-size: 1.1rem;
}

.footer-version {
  color: #00D4FF;
  font-size: 0.72rem;
  font-family: 'Orbitron', monospace;
  letter-spacing: 0.08em;
  background: rgba(0, 212, 255, 0.08);
  padding: 0.15rem 0.45rem;
  border-radius: 3px;
  border: 1px solid rgba(0, 212, 255, 0.15);
}

.footer-right {
  color: #4A5E73;
  font-size: 0.78rem;
}

button {
  font-family: inherit;
}

a {
  color: inherit;
  text-decoration: none;
}

::selection {
  background: rgba(233, 69, 96, 0.4);
  color: #fff;
}

::-moz-selection {
  background: rgba(233, 69, 96, 0.4);
  color: #fff;
}

@media (max-width: 1200px) {
  .main-sidebar {
    display: none;
  }
}

@media (max-width: 768px) {
  .main-container {
    padding: 1.5rem 1rem;
    gap: 1.5rem;
  }
  
  .app-footer {
    padding: 1rem;
  }
  
  .footer-content {
    flex-direction: column;
    gap: 0.75rem;
    text-align: center;
  }
}
</style>