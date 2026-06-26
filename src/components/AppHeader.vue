<template>
  <header class="header">
    <div class="header-left">
      <div class="logo-mark">
        <span class="logo-hex"></span>
      </div>
      <span class="logo-text">{{ i18n.t('app.title') }}</span>
      <span class="version-tag">{{ i18n.t('app.version') }}</span>
    </div>

    <button class="mobile-menu-btn" aria-label="Menu" @click="toggleMenu">
      <span></span><span></span><span></span>
    </button>

    <nav class="nav" :class="{ open: menuOpen }">
      <router-link
        v-for="item in navItems"
        :key="item.path"
        :to="item.path"
        class="nav-btn"
        :class="{ active: $route.path === item.path }"
      >
        <span class="nav-dot"></span>
        {{ i18n.t(item.key) }}
      </router-link>

      <span class="nav-sep"></span>

      <button class="lang-btn" @click="i18n.toggleLanguage()">
        {{ i18n.currentLang === 'zh' ? '中' : 'EN' }}
      </button>
    </nav>
  </header>
</template>

<script setup>
import { ref } from 'vue'
import { useI18nStore } from '../stores/i18n'

const i18n = useI18nStore()
const menuOpen = ref(false)

const navItems = [
  { path: '/guide', key: 'nav.guide' },
  { path: '/canvas', key: 'nav.canvas' },
  { path: '/blueprints', key: 'nav.blueprints' },
  { path: '/items', key: 'nav.items' },
  { path: '/recipes', key: 'nav.recipes' },
  { path: '/calculator', key: 'nav.calculator' }
]

function toggleMenu() {
  menuOpen.value = !menuOpen.value
}
</script>

<style scoped>
.header {
  height: 40px;
  padding: 0 1rem;
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  background: rgba(4, 8, 16, 0.96);
  border-bottom: 1px solid rgba(0, 212, 255, 0.15);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  backdrop-filter: blur(16px);
}

.header-left {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  justify-self: start;
}

.logo-mark {
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.logo-hex {
  display: block;
  width: 14px;
  height: 14px;
  background: #00D4FF;
  clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%);
  box-shadow: 0 0 8px rgba(0, 212, 255, 0.5);
  animation: logoPulse 3s ease-in-out infinite;
}
@keyframes logoPulse {
  0%, 100% { box-shadow: 0 0 6px rgba(0, 212, 255, 0.4); }
  50% { box-shadow: 0 0 12px rgba(0, 212, 255, 0.7); }
}

.logo-text {
  color: #00D4FF;
  font-family: 'Orbitron', 'Rajdhani', sans-serif;
  font-size: 0.78rem;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.version-tag {
  color: #4A5E73;
  font-family: 'Orbitron', monospace;
  font-size: 0.55rem;
  letter-spacing: 0.1em;
  background: rgba(0, 212, 255, 0.06);
  padding: 0.1rem 0.35rem;
  border-radius: 3px;
  border: 1px solid rgba(0, 212, 255, 0.1);
  text-transform: uppercase;
}

.nav {
  display: flex;
  align-items: center;
  gap: 0.15rem;
  justify-self: center;
}

.nav-btn {
  position: relative;
  padding: 0.35rem 0.65rem;
  color: #7A8FA6;
  text-decoration: none;
  font-size: 0.73rem;
  font-weight: 500;
  border-radius: 4px;
  transition: all 0.25s ease;
  display: flex;
  align-items: center;
  gap: 0.3rem;
}
.nav-btn:hover {
  color: #E8EEF4;
  background: rgba(0, 212, 255, 0.08);
}
.nav-btn.active {
  color: #00D4FF;
  background: rgba(0, 212, 255, 0.12);
}
.nav-dot {
  display: none;
  width: 4px;
  height: 4px;
  background: #00D4FF;
  border-radius: 50%;
}
.nav-btn.active .nav-dot {
  display: block;
}

.nav-sep {
  width: 1px;
  height: 16px;
  background: rgba(0, 212, 255, 0.12);
  margin: 0 0.25rem;
}

.lang-btn {
  padding: 0.25rem 0.45rem;
  background: rgba(0, 212, 255, 0.06);
  border: 1px solid rgba(0, 212, 255, 0.15);
  color: #7A8FA6;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.65rem;
  font-weight: 600;
  font-family: 'Orbitron', monospace;
  letter-spacing: 0.05em;
  transition: all 0.25s;
}
.lang-btn:hover {
  background: rgba(0, 212, 255, 0.15);
  border-color: rgba(0, 212, 255, 0.35);
  color: #00D4FF;
}

.mobile-menu-btn {
  display: none;
  flex-direction: column;
  gap: 4px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.25rem;
}
.mobile-menu-btn span {
  width: 20px;
  height: 2px;
  background: #7A8FA6;
  border-radius: 1px;
  transition: all 0.3s;
}
.mobile-menu-btn:hover span {
  background: #00D4FF;
}

@media (max-width: 768px) {
  .mobile-menu-btn { display: flex; }

  .nav {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    flex-direction: column;
    padding: 0.75rem 1rem;
    gap: 0.25rem;
    background: rgba(4, 8, 16, 0.98);
    border-bottom: 1px solid rgba(0, 212, 255, 0.2);
    backdrop-filter: blur(16px);
    transform: translateY(-120%);
    opacity: 0;
    visibility: hidden;
    transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  }
  .nav.open {
    transform: translateY(0);
    opacity: 1;
    visibility: visible;
  }
  .nav-btn { width: 100%; text-align: center; justify-content: center; }
  .nav-sep { display: none; }
  .lang-btn { width: 100%; text-align: center; }
}
</style>
