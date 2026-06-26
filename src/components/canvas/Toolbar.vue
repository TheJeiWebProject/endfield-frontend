<template>
  <div class="toolbar">
    <button
      v-for="tool in tools"
      :key="tool.id"
      class="tool-btn"
      :class="{ active: currentTool === tool.id }"
      :title="tool.title"
      @click="$emit('update:currentTool', tool.id)"
    >
      <SvgIcon :name="tool.icon" size="18" />
    </button>

    <button
      class="tool-btn"
      :title="i18n.t('canvas.toolbar.settings')"
      @click="$emit('showSettings')"
    >
      <SvgIcon name="gear" size="18" />
    </button>

    <button
      class="tool-btn"
      :title="showInfoPanel ? i18n.t('toolbar.hidePanel') : i18n.t('toolbar.showPanel')"
      @click="$emit('toggleInfoPanel')"
    >
      <SvgIcon :name="showInfoPanel ? 'chartBar' : 'chartLine'" size="18" />
    </button>

    <button
      class="tool-btn guide-btn"
      :title="i18n.t('canvas.toolbar.guide')"
      @click="$emit('showGuide')"
    >
      <SvgIcon name="help" size="18" />
    </button>

    <button
      class="tool-btn"
      :disabled="!selectedItem"
      @click="$emit('showRecipePanel')"
    >
      <SvgIcon name="book" size="18" />
    </button>

    <!-- 地区选择 -->
    <div class="region-selector">
      <select
        :value="selectedRegion"
        class="region-select"
        @change="handleRegionChange"
      >
        <option
          value=""
          disabled
          :placeholder="i18n.t('canvas.region.select')"
        >
          {{ i18n.t('canvas.region.select') }}
        </option>
        <optgroup :label="i18n.t('canvas.region.tundra')">
          <option
            v-for="region in tundraRegions"
            :key="region.id"
            :value="region.id"
          >
            {{ region.name }} ({{ region.size }})
          </option>
        </optgroup>
        <optgroup :label="i18n.t('canvas.region.jinlong')">
          <option
            v-for="region in jinlongRegions"
            :key="region.id"
            :value="region.id"
          >
            {{ region.name }} ({{ region.size }})
          </option>
        </optgroup>
      </select>
    </div>

    <!-- 信息面板 -->
    <div v-if="showInfoPanel" class="info-panel-container">
      <InfoPanel
        :grid-cols="gridCols"
        :grid-rows="gridRows"
        :grid-size="gridSize"
        :zoom="zoom"
        :placed-count="placedCount"
        :total-power="totalPower"
        :selected-item-name="selectedItemName"
        @close="$emit('toggleInfoPanel')"
      />
    </div>

    <!-- 画布控制 -->
    <div class="canvas-controls">
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.zoom.in')"
        @click="$emit('zoomIn')"
      >
        +
      </button>
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.zoom.out')"
        @click="$emit('zoomOut')"
      >
        −
      </button>
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.reset')"
        @click="$emit('resetView')"
      >
        ⌂
      </button>
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.save')"
        @click="$emit('saveLayout')"
      >
      <SvgIcon name="save" size="16" />
      </button>
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.load')"
        @click="$emit('showLoadPanel')"
      >
      <SvgIcon name="folder" size="16" />
      </button>
      <button
        class="canvas-btn"
        :title="i18n.t('canvas.blueprint')"
        @click="$emit('showBlueprintPanel')"
      >
      <SvgIcon name="ruler" size="16" />
      </button>
    </div>
  </div>
</template>

<script setup>
import { computed, toRefs } from 'vue'
import { useI18nStore } from '../../stores/i18n.js'
import InfoPanel from './InfoPanel.vue'
import SvgIcon from '../icons/SvgIcon.vue'

const i18n = useI18nStore()

defineProps({
  currentTool: { type: String, required: true },
  selectedRegion: { type: String, default: '' },
  tundraRegions: { type: Array, default: () => [] },
  jinlongRegions: { type: Array, default: () => [] },
  showInfoPanel: { type: Boolean, default: true },
  selectedItem: { type: Object, default: null },
  gridCols: { type: Number, default: 60 },
  gridRows: { type: Number, default: 60 },
  gridSize: { type: Number, default: 64 },
  zoom: { type: Number, default: 1 },
  placedCount: { type: Number, default: 0 },
  totalPower: { type: Number, default: 0 },
  selectedItemName: { type: String, default: '无' }
})

const emit = defineEmits([
  'update:currentTool',
  'region-change',
  'toggleInfoPanel',
  'showSettings',
  'showRecipePanel',
  'zoomIn',
  'zoomOut',
  'resetView',
  'saveLayout',
  'showLoadPanel',
  'showBlueprintPanel',
  'setActiveLayer',
  'setShowLayer1',
  'setShowLayer2',
  'showGuide'
])

function handleRegionChange(event) {
  const newRegion = event.target.value
  emit('region-change', newRegion)
}

const tools = computed(() => [
  { id: 'select', icon: 'search', title: i18n.t('canvas.toolbar.select') },
  { id: 'place', icon: 'box', title: i18n.t('canvas.toolbar.place') },
  { id: 'belt', icon: 'refresh', title: i18n.t('canvas.toolbar.belt') },
  { id: 'pipe', icon: 'droplet', title: i18n.t('canvas.toolbar.pipe') },
  { id: 'delete', icon: 'trash', title: i18n.t('canvas.toolbar.delete') }
])
</script>

<style scoped>
.toolbar {
  position: absolute;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: rgba(10, 10, 10, 0.95);
  border: 1px solid rgba(0, 212, 255, 0.3);
  border-radius: 12px;
  z-index: 100;
  backdrop-filter: blur(10px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5), 0 0 20px rgba(0, 212, 255, 0.05);
  flex-wrap: wrap;
}

.tool-btn {
  width: 40px;
  height: 40px;
  border: none;
  border-radius: 8px;
  background: rgba(10, 10, 10, 0.8);
  color: #00D4FF;
  font-size: 20px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(0, 212, 255, 0.3);
}

.tool-btn:hover {
  background: rgba(0, 212, 255, 0.15);
  border-color: rgba(0, 212, 255, 0.5);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(0, 212, 255, 0.2);
}

.tool-btn.active {
  background: rgba(0, 212, 255, 0.3);
  border-color: #00D4FF;
  box-shadow: 0 4px 20px rgba(0, 212, 255, 0.3);
  transform: translateY(-2px);
}

.tool-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.region-selector {
  margin-left: 12px;
  padding-left: 12px;
  border-left: 1px solid rgba(0, 212, 255, 0.3);
}

.region-select {
  padding: 8px 14px;
  border: 1px solid rgba(0, 212, 255, 0.3);
  border-radius: 8px;
  background: rgba(10, 10, 10, 0.9);
  color: #00D4FF;
  font-size: 13px;
  cursor: pointer;
  transition: all 0.3s;
  backdrop-filter: blur(5px);
}

.region-select:hover {
  border-color: rgba(0, 212, 255, 0.5);
  box-shadow: 0 0 15px rgba(0, 212, 255, 0.2);
}

.region-select:focus {
  outline: none;
  border-color: #00D4FF;
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.3);
}

.info-panel-container {
  position: absolute;
  top: 100%;
  left: 0;
  margin-top: 8px;
}

.canvas-controls {
  display: flex;
  gap: 6px;
  margin-left: 12px;
  padding-left: 12px;
  border-left: 1px solid rgba(0, 212, 255, 0.3);
}

.canvas-btn {
  width: 36px;
  height: 36px;
  border: none;
  border-radius: 8px;
  background: rgba(10, 10, 10, 0.9);
  color: #00D4FF;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(0, 212, 255, 0.3);
}

.canvas-btn:hover {
  background: rgba(0, 212, 255, 0.15);
  border-color: rgba(0, 212, 255, 0.5);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(0, 212, 255, 0.2);
}

.layer-controls {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-left: 12px;
  padding-left: 12px;
  border-left: 1px solid rgba(0, 212, 255, 0.3);
}

.layer-toggle {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2px;
}

.layer-btn {
  width: 36px;
  height: 36px;
  border: none;
  border-radius: 8px;
  background: linear-gradient(145deg, rgba(10, 16, 32, 0.8), rgba(22, 33, 62, 0.6));
  color: #eaeaea;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(10, 16, 32, 0.4);
}

.layer-btn:hover {
  background: linear-gradient(145deg, rgba(0, 212, 255, 0.3), rgba(10, 16, 32, 0.7));
  border-color: rgba(0, 212, 255, 0.5);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(0, 212, 255, 0.3);
}

.layer-btn.active {
  background: linear-gradient(135deg, #00D4FF, #0088AA);
  border-color: #00D4FF;
  box-shadow: 0 4px 20px rgba(0, 212, 255, 0.5);
  transform: translateY(-2px);
}

.layer-label {
  font-size: 10px;
  color: #aaa;
}

.layer-divider {
  width: 1px;
  height: 24px;
  background: rgba(0, 212, 255, 0.3);
  margin: 0 4px;
}

.layer-checkbox-label {
  display: flex;
  align-items: center;
  gap: 4px;
  cursor: pointer;
  font-size: 11px;
  color: #aaa;
  user-select: none;
}

.layer-checkbox-label input[type="checkbox"] {
  width: 14px;
  height: 14px;
  cursor: pointer;
  accent-color: #00D4FF;
}

.checkbox-text {
  font-size: 11px;
}
</style>
