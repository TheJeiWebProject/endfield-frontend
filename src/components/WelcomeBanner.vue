<template>
  <transition name="banner-fade">
    <div v-if="showBanner" class="welcome-banner">
      <div class="banner-content">
        <div class="welcome-text">{{ i18n.t('welcome.text') }}</div>
        <div class="contact-text">{{ i18n.t('welcome.contact') }}</div>
      </div>
      <button class="banner-close" @click="dismissBanner" :title="i18n.t('welcome.closeHint')">X</button>
    </div>
  </transition>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useI18nStore } from '../stores/i18n'

const i18n = useI18nStore()
const showBanner = ref(false)

function dismissBanner() {
  showBanner.value = false
  localStorage.setItem('endfield_has_visited', 'true')
}

onMounted(() => {
  const hasVisited = localStorage.getItem('endfield_has_visited')
  if (!hasVisited) {
    showBanner.value = true
  }

  window.clearVisitFlag = () => {
    localStorage.removeItem('endfield_has_visited')
    console.log('Visit flag cleared. Refresh page to see welcome banner again.')
  }
})
</script>

<style scoped>
.welcome-banner {
  background: linear-gradient(135deg, rgba(45, 45, 68, 0.9), rgba(31, 31, 58, 0.85));
  border-bottom: 2px solid rgba(0, 212, 255, 0.5);
  padding: 0.75rem 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  z-index: 100;
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 20px rgba(0, 212, 255, 0.2);
  animation: slideDown 0.5s ease-out;
}

.banner-content {
  text-align: center;
}

.banner-close {
  position: absolute;
  right: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(0, 212, 255, 0.25);
  color: #7A8FA6;
  width: 24px;
  height: 24px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  line-height: 1;
}
.banner-close:hover {
  background: rgba(0, 212, 255, 0.18);
  color: #00D4FF;
  border-color: rgba(0, 212, 255, 0.5);
}

.banner-fade-leave-active {
  transition: all 0.3s ease;
}
.banner-fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

@keyframes slideDown {
  from { transform: translateY(-100%); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.welcome-text {
  color: #00D4FF;
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 0.25rem;
  text-shadow: 0 0 20px rgba(0, 212, 255, 0.6);
  animation: glow 2s ease-in-out infinite alternate;
}

@keyframes glow {
  from { text-shadow: 0 0 10px rgba(0, 212, 255, 0.4), 0 0 20px rgba(0, 212, 255, 0.2); }
  to { text-shadow: 0 0 20px rgba(0, 212, 255, 0.8), 0 0 30px rgba(0, 212, 255, 0.4); }
}

.contact-text {
  color: #f0f0f0;
  font-size: 0.9rem;
  background: rgba(31, 31, 58, 0.4);
  padding: 0.3rem 0.75rem;
  border-radius: 20px;
  display: inline-block;
  border: 1px solid rgba(0, 212, 255, 0.2);
  backdrop-filter: blur(5px);
}

@media (max-width: 768px) {
  .welcome-banner { padding: 0.6rem 1.5rem; }
  .welcome-text { font-size: 1rem; }
  .contact-text { font-size: 0.8rem; }
}
</style>
