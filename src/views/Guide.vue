<template>
  <div class="guide-container">
    <div class="section-header">
      <h2>{{ i18n.t('guide.title') }}</h2>
      <p>{{ i18n.t('guide.description') }}</p>
      <button class="restart-tutorial-btn" @click="handleRestartTutorial">
        <span class="btn-icon">&#9654;</span>
        {{ i18n.t('guide.restartTutorial') || '重新查看交互式教程' }}
      </button>
    </div>

    <div class="guide-content">
      <div class="guide-section card">
        <h3>{{ i18n.t('guide.features.title') }}</h3>
        <p>{{ i18n.t('guide.features.text') }}</p>
        <div class="feature-list">
          <div class="feature-item">
            <div>
              <strong>{{ i18n.t('home.feature.canvas') }}</strong>
              <p>{{ i18n.t('home.feature.canvas.desc') }}</p>
            </div>
          </div>
          <div class="feature-item">
            <div>
              <strong>{{ i18n.t('home.feature.items') }}</strong>
              <p>{{ i18n.t('home.feature.items.desc') }}</p>
            </div>
          </div>
          <div class="feature-item">
            <div>
              <strong>{{ i18n.t('home.feature.recipes') }}</strong>
              <p>{{ i18n.t('home.feature.recipes.desc') }}</p>
            </div>
          </div>
          <div class="feature-item">
            <div>
              <strong>{{ i18n.t('home.feature.calculator') }}</strong>
              <p>{{ i18n.t('home.feature.calculator.desc') }}</p>
            </div>
          </div>
        </div>
      </div>

      <div class="guide-section card">
        <h3>{{ i18n.t('guide.v21.title') }}</h3>
        <div class="changelog-list">
          <div class="changelog-item" v-for="(item, index) in changelogItems" :key="index">
            <span class="changelog-dot"></span>
            <span>{{ item }}</span>
          </div>
        </div>
      </div>

      <div class="guide-section card">
        <h3>{{ i18n.t('guide.canvas.title') }}</h3>
        <p>{{ i18n.t('guide.canvas.text') }}</p>
        <ul class="guide-list">
          <li>{{ i18n.t('guide.canvas.drag') }}</li>
          <li>{{ i18n.t('guide.canvas.zoom') }}</li>
          <li><span class="key-hint">{{ i18n.t('guide.shortcuts.select') }}</span> {{ i18n.t('guide.canvas.select') }}</li>
          <li><span class="key-hint">{{ i18n.t('guide.shortcuts.pan') }}</span> {{ i18n.t('guide.canvas.pan') }}</li>
          <li>{{ i18n.t('guide.canvas.rotate') }}</li>
          <li>{{ i18n.t('guide.canvas.delete') }}</li>
          <li>{{ i18n.t('guide.canvas.continuous') }}</li>
        </ul>
      </div>

      <div class="guide-section card">
        <h3>{{ i18n.t('guide.calculator.title') }}</h3>
        <p>{{ i18n.t('guide.calculator.text') }}</p>
        <ul class="guide-list">
          <li>{{ i18n.t('guide.calculator.target') }}</li>
          <li>{{ i18n.t('guide.calculator.materials') }}</li>
          <li>{{ i18n.t('guide.calculator.power') }}</li>
          <li>{{ i18n.t('guide.calculator.tree') }}</li>
        </ul>
      </div>

      <div class="guide-section card shortcuts-section">
        <h3>{{ i18n.t('guide.shortcuts.title') }}</h3>
        <div class="shortcut-grid">
          <div class="shortcut-item" v-for="shortcut in shortcuts" :key="shortcut.key">
            <kbd>{{ shortcut.key }}</kbd>
            <span>{{ shortcut.label }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, inject } from 'vue'
import { useI18nStore } from '../stores/i18n'

const i18n = useI18nStore()
const restartTutorial = inject('restartTutorial', () => {})

function handleRestartTutorial() {
  restartTutorial()
}

const changelogItems = computed(() => [
  i18n.t('guide.v21.selection'),
  i18n.t('guide.v21.delete'),
  i18n.t('guide.v21.isolation'),
  i18n.t('guide.v21.continuous'),
  i18n.t('guide.v21.smart'),
  i18n.t('guide.v21.visual')
])

const shortcuts = computed(() => [
  { key: 'V', label: i18n.t('guide.shortcuts.select') },
  { key: 'P', label: i18n.t('guide.shortcuts.place') },
  { key: 'B', label: i18n.t('guide.shortcuts.belt') },
  { key: 'N', label: i18n.t('guide.shortcuts.pipe') },
  { key: 'X', label: i18n.t('guide.shortcuts.delete') },
  { key: 'R', label: i18n.t('guide.shortcuts.rotate') },
  { key: 'Delete', label: i18n.t('guide.shortcuts.deleteSelected') },
  { key: 'Shift + Drag', label: i18n.t('guide.shortcuts.pan') },
  { key: 'Drag', label: i18n.t('guide.shortcuts.selectBox') },
  { key: 'Scroll', label: i18n.t('guide.shortcuts.zoom') },
  { key: 'Ctrl + S', label: i18n.t('guide.shortcuts.save') },
  { key: 'Ctrl + R', label: i18n.t('guide.shortcuts.reset') }
])
</script>

<style scoped>
.guide-container {
  max-width: 900px;
  margin: 0 auto;
}

.guide-content {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.guide-section {
  position: relative;
  padding: 1.75rem;
}

.guide-section h3 {
  color: var(--primary);
  font-size: 1.3rem;
  margin-bottom: 1.25rem;
  font-weight: 600;
}

.guide-section p {
  color: #b0b0b0;
  line-height: 1.7;
  margin-bottom: 1.25rem;
  font-size: 1rem;
}

/* 功能列表 */
.feature-list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1rem;
}

.feature-item {
  display: flex;
  gap: 0.85rem;
  padding: 1rem;
  background: var(--bg-card);
  clip-path: polygon(0 0, calc(100% - 8px) 0, 100% 8px, 100% 100%, 8px 100%, 0 calc(100% - 8px));
  border: 1px solid rgba(0, 212, 255, 0.2);
  transition: all 0.3s;
}

.feature-item:hover {
  border-color: rgba(0, 212, 255, 0.4);
  background: rgba(0, 212, 255, 0.08);
  transform: translateY(-3px);
}

.feature-icon {
  font-size: 1.5rem;
  flex-shrink: 0;
}

.feature-item strong {
  color: #f5f5f5;
  display: block;
  margin-bottom: 0.35rem;
  font-weight: 600;
}

.feature-item p {
  color: #909090;
  font-size: 0.88rem;
  line-height: 1.5;
  margin: 0;
}

/* 更新日志列表 */
.changelog-list {
  display: flex;
  flex-direction: column;
  gap: 0.85rem;
}

.changelog-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.65rem 0;
  color: #ccc;
  font-size: 0.95rem;
}

.changelog-dot {
  width: 8px;
  height: 8px;
  background: linear-gradient(135deg, #00D4FF, #ff8c42);
  border-radius: 50%;
  flex-shrink: 0;
  box-shadow: 0 0 8px rgba(0, 212, 255, 0.4);
}

/* 指南列表 */
.guide-list {
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.guide-list li {
  color: #ccc;
  padding: 0.6rem 0;
  padding-left: 1.5rem;
  position: relative;
  font-size: 0.95rem;
  border-bottom: 1px solid rgba(0, 212, 255, 0.1);
}

.guide-list li:last-child {
  border-bottom: none;
}

.guide-list li::before {
  content: '▸';
  color: var(--primary);
  position: absolute;
  left: 0;
}

.key-hint {
  display: inline-block;
  background: rgba(78, 205, 196, 0.2);
  color: #4ecdc4;
  padding: 0.15rem 0.5rem;
  border-radius: 4px;
  font-size: 0.8rem;
  font-family: monospace;
  margin-right: 0.5rem;
  border: 1px solid rgba(78, 205, 196, 0.4);
}

/* 快捷键区域 */
.shortcuts-section {
  overflow: hidden;
}

.shortcut-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 0.75rem;
}

.shortcut-item {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  padding: 0.85rem 1rem;
  background: var(--bg-card);
  border-radius: 10px;
  border: 1px solid rgba(0, 212, 255, 0.2);
  transition: all 0.3s;
}

.shortcut-item:hover {
  border-color: rgba(0, 212, 255, 0.4);
  background: rgba(0, 212, 255, 0.08);
  transform: translateY(-2px);
}

kbd {
  background: var(--bg-card);
  border: 1px solid rgba(0, 212, 255, 0.4);
  color: var(--primary);
  padding: 0.35rem 0.6rem;
  border-radius: 6px;
  font-family: 'SF Mono', 'Monaco', 'Consolas', monospace;
  font-size: 0.88rem;
  white-space: nowrap;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  min-width: 32px;
  text-align: center;
}

.shortcut-item span {
  color: #ccc;
  font-size: 0.9rem;
  line-height: 1.3;
}

/* Restart tutorial button */
.restart-tutorial-btn {
  margin-top: 0.75rem;
  padding: 0.55rem 1.25rem;
  background: rgba(0, 212, 255, 0.1);
  border: 1px solid rgba(0, 212, 255, 0.3);
  color: #00D4FF;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.9rem;
  font-weight: 500;
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  transition: all 0.3s;
}
.restart-tutorial-btn:hover {
  background: rgba(0, 212, 255, 0.2);
  border-color: rgba(0, 212, 255, 0.5);
  transform: translateY(-1px);
}
.btn-icon {
  font-size: 0.8rem;
}

@media (max-width: 768px) {
  .guide-container {
    padding: 0 0.5rem;
  }

  .guide-section {
    padding: 1.25rem;
  }

  .guide-section h3 {
    font-size: 1.1rem;
  }

  .section-icon {
    font-size: 1.4rem;
    top: 0.75rem;
    right: 1rem;
  }

  .feature-list {
    grid-template-columns: 1fr;
    gap: 0.75rem;
  }

  .feature-item {
    padding: 0.85rem;
  }

  .shortcut-grid {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 0.5rem;
  }

  .shortcut-item {
    padding: 0.65rem 0.8rem;
  }

  kbd {
    font-size: 0.8rem;
    padding: 0.25rem 0.5rem;
  }
}

@media (max-width: 480px) {
  .guide-section {
    padding: 1rem;
  }

  .guide-section p {
    font-size: 0.9rem;
  }

  .shortcut-grid {
    grid-template-columns: 1fr;
  }

  .guide-list li {
    font-size: 0.9rem;
  }
}
</style>
