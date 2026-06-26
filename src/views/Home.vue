<template>
  <div class="home-container">
    <div class="welcome-text">
      <p class="welcome-line">
        {{ i18n.t('home.welcome') }}
      </p>
      <p class="contact-line">
        {{ i18n.t('home.contact') }}
      </p>
    </div>
    
    <h1 class="home-title animate-title-reveal">
      {{ i18n.t('home.title') }}
    </h1>
    <p class="home-subtitle">
      {{ i18n.t('home.subtitle') }}
    </p>

    <div class="feature-grid">
      <router-link
        v-for="(feature, index) in features"
        :key="feature.path"
        :to="feature.path"
        class="feature-card card-stagger"
        :style="{ '--card-delay': `${index * 100}ms` }"
      >
        <div class="feature-icon">
          <SvgIcon :name="feature.icon" size="48" />
        </div>
        <h2 class="feature-title">
          {{ i18n.t(feature.titleKey) }}
        </h2>
        <p class="feature-desc">
          {{ i18n.t(feature.descKey) }}
        </p>
      </router-link>
    </div>

    <div class="stats">
      <h3>{{ i18n.t('home.stats.title') }}</h3>
      <div class="stats-grid">
        <div class="stat-item">
          <div class="stat-value">
            {{ dataStore.items.length }}
          </div>
          <div class="stat-label">
            {{ i18n.t('home.stats.items') }}
          </div>
        </div>
        <div class="stat-item">
          <div class="stat-value">
            {{ dataStore.recipes.length }}
          </div>
          <div class="stat-label">
            {{ i18n.t('home.stats.recipes') }}
          </div>
        </div>
        <div class="stat-item">
          <div class="stat-value">
            {{ dataStore.icons.length }}
          </div>
          <div class="stat-label">
            {{ i18n.t('home.stats.icons') }}
          </div>
        </div>
        <div class="stat-item">
          <div class="stat-value">
            {{ machineCount }}
          </div>
          <div class="stat-label">
            {{ i18n.t('home.stats.machines') }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useDataStore } from '../stores/data'
import { useI18nStore } from '../stores/i18n'
import SvgIcon from '../components/icons/SvgIcon.vue'

const dataStore = useDataStore()
const i18n = useI18nStore()

const features = [
  {
    path: '/canvas',
    icon: 'palette',
    titleKey: 'home.feature.canvas',
    descKey: 'home.feature.canvas.desc'
  },
  {
    path: '/items',
    icon: 'box',
    titleKey: 'home.feature.items',
    descKey: 'home.feature.items.desc'
  },
  {
    path: '/recipes',
    icon: 'clipboard',
    titleKey: 'home.feature.recipes',
    descKey: 'home.feature.recipes.desc'
  },
  {
    path: '/calculator',
    icon: 'calculator',
    titleKey: 'home.feature.calculator',
    descKey: 'home.feature.calculator.desc'
  }
]

const machineCount = computed(() => {
  return dataStore.items.filter(item =>
    item.machine || item.category === 'machine'
  ).length
})
</script>

<style scoped>
.home-container {
  position: relative;
  text-align: center;
  padding: 2.5rem 1rem;
  animation: fadeIn 0.6s ease-out;
  overflow: hidden;
  background: var(--bg-card);
  border: 1px solid var(--primary-border);
  clip-path: polygon(0 0, calc(100% - var(--clip-xl)) 0, 100% var(--clip-xl), 100% 100%, var(--clip-xl) 100%, 0 calc(100% - var(--clip-xl)));
  backdrop-filter: blur(20px);
}

.home-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.5), transparent);
  animation: headerSweep 6s ease-in-out infinite;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.welcome-text {
  margin-bottom: 1.5rem;
}

.welcome-line {
  font-size: 0.85rem;
  font-family: var(--font-display);
  color: var(--primary);
  margin-bottom: 0.25rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.contact-line {
  font-size: 0.75rem;
  color: var(--text-dim);
  margin: 0;
}

.home-title {
  font-size: 2.8rem;
  font-family: var(--font-display);
  background: linear-gradient(135deg, #00D4FF, #00FF88);
  background-size: 200% auto;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: shimmer 3s linear infinite;
  margin-bottom: 0.5rem;
  font-weight: 700;
  letter-spacing: 0.06em;
}

@keyframes shimmer {
  to {
    background-position: 200% center;
  }
}

.home-subtitle {
  font-size: 1.1rem;
  color: var(--text-secondary);
  margin-bottom: 3rem;
  font-weight: 400;
  letter-spacing: 0.04em;
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 1.5rem;
  margin-bottom: 3rem;
}

.feature-card {
  background: var(--bg-card);
  border: 1px solid var(--primary-border);
  clip-path: polygon(0 0, calc(100% - 12px) 0, 100% 12px, 100% 100%, 12px 100%, 0 calc(100% - 12px));
  padding: 2rem;
  text-align: center;
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  text-decoration: none;
  color: inherit;
  box-shadow: var(--shadow-lg);
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(12px);
}

.feature-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.04), transparent);
  transition: left 0.7s ease;
}

.feature-card:hover::before {
  left: 100%;
}

.feature-card:hover {
  border-color: var(--primary-border-hover);
  transform: translateY(-10px) scale(1.02);
  box-shadow: 0 20px 50px rgba(0, 212, 255, 0.12), 0 0 30px rgba(0, 212, 255, 0.05);
}

.feature-icon {
  font-size: 3.5rem;
  margin-bottom: 1.25rem;
  position: relative;
  filter: drop-shadow(0 4px 10px rgba(0, 212, 255, 0.3));
  animation: float 3s ease-in-out infinite;
}

.feature-icon::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  border: 1px solid rgba(0, 212, 255, 0.15);
  transform: translate(-50%, -50%);
  animation: ringPulse 3s ease-in-out infinite;
  pointer-events: none;
}

@keyframes ringPulse {
  0%, 100% { opacity: 0.3; transform: translate(-50%, -50%) scale(1); }
  50% { opacity: 0.6; transform: translate(-50%, -50%) scale(1.15); }
}

@keyframes float {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

.feature-title {
  color: var(--primary);
  margin-bottom: 0.75rem;
  font-size: 1.3rem;
  font-weight: 600;
  font-family: var(--font-display);
  letter-spacing: 0.06em;
  text-shadow: 0 0 15px rgba(0, 212, 255, 0.3);
}

.feature-desc {
  color: var(--text-secondary);
  line-height: 1.7;
  font-size: 0.95rem;
}

.stats {
  background: var(--bg-card);
  padding: 2.5rem;
  clip-path: polygon(0 0, calc(100% - 14px) 0, 100% 14px, 100% 100%, 14px 100%, 0 calc(100% - 14px));
  border: 1px solid var(--primary-border);
  box-shadow: var(--shadow-lg);
  backdrop-filter: blur(12px);
}

.stats h3 {
  color: var(--primary);
  margin-bottom: 1.5rem;
  font-family: var(--font-display);
  font-size: 1.2rem;
  font-weight: 600;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1.5rem;
}

.stat-item {
  text-align: center;
  padding: 1rem;
  background: rgba(0, 212, 255, 0.03);
  clip-path: polygon(0 0, calc(100% - 8px) 0, 100% 8px, 100% 100%, 8px 100%, 0 calc(100% - 8px));
  border: 1px solid rgba(0, 212, 255, 0.08);
  transition: all 0.3s;
}

.stat-item:hover {
  background: rgba(0, 212, 255, 0.08);
  border-color: var(--primary-border-hover);
  transform: translateY(-5px);
}

.stat-value {
  font-size: 2.8rem;
  font-weight: bold;
  font-family: var(--font-display);
  background: linear-gradient(135deg, #00D4FF, #00AAFF);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 0.25rem;
}

.stat-label {
  color: var(--text-secondary);
  font-size: 0.85rem;
  font-weight: 500;
  letter-spacing: 0.04em;
}

@media (max-width: 768px) {
  .home-title {
    font-size: 1.6rem;
  }

  .home-subtitle {
    font-size: 0.9rem;
    margin-bottom: 2rem;
  }

  .feature-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .feature-card {
    padding: 1.25rem;
  }

  .feature-icon {
    font-size: 2.5rem;
  }

  .feature-title {
    font-size: 1.2rem;
  }

  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 1rem;
  }

  .stat-value {
    font-size: 1.8rem;
  }
}

@media (max-width: 480px) {
  .home-title {
    font-size: 1.4rem;
  }

  .stat-value {
    font-size: 1.5rem;
  }
}
</style>
