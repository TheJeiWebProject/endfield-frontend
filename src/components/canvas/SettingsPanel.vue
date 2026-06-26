<template>
  <div
    class="modal-overlay"
    @click.self="$emit('close')"
  >
    <div class="modal-content">
      <div class="modal-header">
        <h3>{{ i18n.t('canvas.settings.title') }}</h3>
        <button
          class="close-btn"
          @click="$emit('close')"
        >
          <SvgIcon name="close" size="18" />
        </button>
      </div>
      
      <div class="modal-body">
        <div class="form-group">
          <label>{{ i18n.t('canvas.settings.gridCols') }}</label>
          <input 
            type="number" 
            :value="gridCols"
            min="10"
            max="200"
            @input="$emit('update:gridCols', parseInt($event.target.value))"
          >
        </div>
        
        <div class="form-group">
          <label>{{ i18n.t('canvas.settings.gridRows') }}</label>
          <input 
            type="number" 
            :value="gridRows"
            min="10"
            max="200"
            @input="$emit('update:gridRows', parseInt($event.target.value))"
          >
        </div>
        
        <div class="form-group">
          <label>{{ i18n.t('canvas.settings.gridSize') }}</label>
          <input 
            type="number" 
            :value="gridSize"
            min="32"
            max="128"
            step="8"
            @input="$emit('update:gridSize', parseInt($event.target.value))"
          >
        </div>
        
        <div class="form-group">
          <label>{{ i18n.t('canvas.settings.gridOpacity') }}</label>
          <input 
            type="range" 
            :value="gridOpacity"
            min="0"
            max="1"
            step="0.1"
            @input="$emit('update:gridOpacity', parseFloat($event.target.value))"
          >
          <span class="range-value">{{ Math.round(gridOpacity * 100) }}%</span>
        </div>
        
        <div class="form-group checkbox">
          <label>
            <input 
              type="checkbox" 
              :checked="showGrid"
              @change="$emit('update:showGrid', $event.target.checked)"
            >
            {{ i18n.t('canvas.settings.showGrid') }}
          </label>
        </div>

        <!-- 武陵地区图层设置 -->
        <div
          v-if="isWuling"
          class="form-group layer-settings"
        >
          <label class="layer-settings-label">{{ i18n.t('canvas.settings.layer.label') }}</label>
          <div class="layer-options">
            <div class="layer-option">
              <label>
                <input
                  type="radio"
                  name="activeLayer"
                  value="all"
                  :checked="activeLayer === 'all'"
                  @change="$emit('setActiveLayer', 'all')"
                >
                <span>{{ i18n.t('canvas.settings.layer.all') }}</span>
              </label>
            </div>
            <div class="layer-option">
              <label>
                <input
                  type="radio"
                  name="activeLayer"
                  value="layer1"
                  :checked="activeLayer === 'layer1'"
                  @change="$emit('setActiveLayer', 'layer1')"
                >
                <span>{{ i18n.t('canvas.settings.layer.belt') }}</span>
              </label>
            </div>
            <div class="layer-option">
              <label>
                <input
                  type="radio"
                  name="activeLayer"
                  value="layer2"
                  :checked="activeLayer === 'layer2'"
                  @change="$emit('setActiveLayer', 'layer2')"
                >
                <span>{{ i18n.t('canvas.settings.layer.pipe') }}</span>
              </label>
            </div>
          </div>
          <div class="layer-visibility">
            <label class="layer-checkbox">
              <input
                type="checkbox"
                :checked="showLayer1"
                @change="$emit('setShowLayer1', $event.target.checked)"
              >
              <span>{{ i18n.t('canvas.layer1.visible') }}</span>
            </label>
            <label class="layer-checkbox">
              <input
                type="checkbox"
                :checked="showLayer2"
                @change="$emit('setShowLayer2', $event.target.checked)"
              >
              <span>{{ i18n.t('canvas.layer2.visible') }}</span>
            </label>
          </div>
        </div>
      </div>
      
      <div class="modal-footer">
        <button
          class="btn secondary"
          @click="$emit('close')"
        >
          {{ i18n.t('canvas.settings.cancel') }}
        </button>
        <button
          class="btn primary"
          @click="$emit('apply')"
        >
          {{ i18n.t('canvas.settings.apply') }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { inject } from 'vue'
import SvgIcon from '../icons/SvgIcon.vue'


defineProps({
  gridCols: { type: Number, default: 60 },
  gridRows: { type: Number, default: 60 },
  gridSize: { type: Number, default: 64 },
  gridOpacity: { type: Number, default: 0.2 },
  showGrid: { type: Boolean, default: true },
  isWuling: { type: Boolean, default: false },
  activeLayer: { type: String, default: 'all' },
  showLayer1: { type: Boolean, default: true },
  showLayer2: { type: Boolean, default: true }
})
const i18n = inject('i18n')

defineEmits([
  'update:gridCols',
  'update:gridRows',
  'update:gridSize',
  'update:gridOpacity',
  'update:showGrid',
  'apply',
  'close',
  'setActiveLayer',
  'setShowLayer1',
  'setShowLayer2'
])
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(4px);
}

.modal-content {
  width: 420px;
  max-height: 80vh;
  background: #0F1A2E;
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 8px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  box-shadow: 0 8px 40px rgba(0, 0, 0, 0.6);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background: #0A1020;
  border-bottom: 1px solid rgba(0, 212, 255, 0.12);
}

.modal-header h3 {
  margin: 0;
  color: #00D4FF;
  font-size: 14px;
  font-weight: 600;
}

.close-btn {
  width: 26px; height: 26px;
  border: 1px solid rgba(0, 212, 255, 0.15);
  border-radius: 4px;
  background: transparent;
  color: #7A8FA6;
  cursor: pointer;
  font-size: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}
.close-btn:hover {
  background: rgba(0, 212, 255, 0.1);
  color: #00D4FF;
  border-color: rgba(0, 212, 255, 0.35);
}

.modal-body {
  flex: 1;
  overflow-y: auto;
  padding: 16px;
}

.modal-footer {
  display: flex;
  gap: 8px;
  padding: 12px 16px;
  background: #0A1020;
  border-top: 1px solid rgba(0, 212, 255, 0.12);
  justify-content: flex-end;
}

.form-group {
  margin-bottom: 12px;
}

.form-group label {
  display: block;
  margin-bottom: 6px;
  color: #7A8FA6;
  font-size: 12px;
}

.form-group input[type="number"],
.form-group input[type="range"] {
  width: 100%;
  padding: 6px 10px;
  border: 1px solid rgba(0, 212, 255, 0.18);
  border-radius: 4px;
  background: #040810;
  color: #E8EEF4;
  font-size: 13px;
  accent-color: #00D4FF;
}
.form-group input:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.4);
}

.checkbox-group {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}
.checkbox-group input[type="checkbox"] {
  accent-color: #00D4FF;
  width: 14px; height: 14px;
}
.checkbox-group label {
  color: #E8EEF4;
  font-size: 12px;
  margin: 0;
}

.layer-settings {
  margin-top: 8px;
  padding: 10px;
  background: #0A1020;
  border: 1px solid rgba(0, 212, 255, 0.1);
  border-radius: 6px;
}
.layer-settings-label {
  display: block;
  margin-bottom: 8px;
  color: #7A8FA6;
  font-size: 11px;
  font-weight: 600;
}

.layer-option {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 6px;
  padding: 6px 8px;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}
.layer-option:hover {
  background: rgba(0, 212, 255, 0.05);
}
.layer-option input[type="radio"] {
  accent-color: #00D4FF;
}
.layer-option span {
  color: #7A8FA6;
  font-size: 12px;
}

.btn {
  padding: 7px 16px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  transition: all 0.2s;
  border: 1px solid transparent;
}
.btn.secondary {
  background: transparent;
  border: 1px solid rgba(0, 212, 255, 0.2);
  color: #7A8FA6;
}
.btn.secondary:hover {
  background: rgba(0, 212, 255, 0.08);
  color: #00D4FF;
}
.btn.primary {
  background: rgba(0, 212, 255, 0.12);
  border: 1px solid rgba(0, 212, 255, 0.25);
  color: #00D4FF;
}
.btn.primary:hover {
  background: rgba(0, 212, 255, 0.25);
}

.modal-body::-webkit-scrollbar { width: 4px; }
.modal-body::-webkit-scrollbar-track { background: transparent; }
.modal-body::-webkit-scrollbar-thumb { background: rgba(0, 212, 255, 0.2); border-radius: 2px; }
</style>