<template>
  <div class="pipeline-drag-settings">
    <h3>{{ i18n.t('pipelineSettings.title') }}</h3>

    <div class="setting-section">
      <h4>{{ i18n.t('pipelineSettings.layout') }}</h4>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.direction') }}</label>
        <select v-model="localConfig.layout.direction">
          <option value="down">{{ i18n.t('pipelineSettings.down') }}</option>
          <option value="up">{{ i18n.t('pipelineSettings.up') }}</option>
        </select>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.layerSpacing') }}</label>
        <input type="number" v-model.number="localConfig.layout.layerSpacing" min="0" max="5">
        <span class="unit">{{ i18n.t('pipelineSettings.grid') }}</span>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.machineSpacing') }}</label>
        <input type="number" v-model.number="localConfig.layout.machineSpacing" min="0" max="5">
        <span class="unit">{{ i18n.t('pipelineSettings.grid') }}</span>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.machineWidth') }}</label>
        <input type="number" v-model.number="localConfig.layout.machineSize.width" min="1" max="10">
        <span class="unit">{{ i18n.t('pipelineSettings.grid') }}</span>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.machineHeight') }}</label>
        <input type="number" v-model.number="localConfig.layout.machineSize.height" min="1" max="10">
        <span class="unit">{{ i18n.t('pipelineSettings.grid') }}</span>
      </div>
    </div>

    <div class="setting-section">
      <h4>{{ i18n.t('pipelineSettings.preview') }}</h4>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.validColor') }}</label>
        <input type="color" v-model="localConfig.preview.validColor">
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.invalidColor') }}</label>
        <input type="color" v-model="localConfig.preview.invalidColor">
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.borderWidth') }}</label>
        <input type="number" v-model.number="localConfig.preview.borderWidth" min="1" max="10">
        <span class="unit">{{ i18n.t('pipelineSettings.px') }}</span>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.borderStyle') }}</label>
        <select v-model="localConfig.preview.borderStyle">
          <option value="solid">{{ i18n.t('pipelineSettings.solid') }}</option>
          <option value="dashed">{{ i18n.t('pipelineSettings.dashed') }}</option>
        </select>
      </div>
    </div>

    <div class="setting-section">
      <h4>{{ i18n.t('pipelineSettings.placement') }}</h4>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.order') }}</label>
        <select v-model="localConfig.placement.order">
          <option value="bottom-up">{{ i18n.t('pipelineSettings.bottomUp') }}</option>
          <option value="top-down">{{ i18n.t('pipelineSettings.topDown') }}</option>
        </select>
      </div>
    </div>

    <div class="setting-section">
      <h4>{{ i18n.t('pipelineSettings.collision') }}</h4>
      <div class="setting-item checkbox">
        <input type="checkbox" id="checkBounds" v-model="localConfig.collision.checkBounds">
        <label for="checkBounds">{{ i18n.t('pipelineSettings.checkBounds') }}</label>
      </div>
      <div class="setting-item checkbox">
        <input type="checkbox" id="checkMachineCollision" v-model="localConfig.collision.checkMachineCollision">
        <label for="checkMachineCollision">{{ i18n.t('pipelineSettings.checkMachine') }}</label>
      </div>
    </div>

    <div class="setting-section">
      <h4>{{ i18n.t('pipelineSettings.debug') }}</h4>
      <div class="setting-item checkbox">
        <input type="checkbox" id="debugEnabled" v-model="localConfig.debug.enabled">
        <label for="debugEnabled">{{ i18n.t('pipelineSettings.enableDebug') }}</label>
      </div>
      <div class="setting-item">
        <label>{{ i18n.t('pipelineSettings.logLevel') }}</label>
        <select v-model="localConfig.debug.logLevel">
          <option value="info">{{ i18n.t('pipelineSettings.info') }}</option>
          <option value="debug">{{ i18n.t('pipelineSettings.debugLevel') }}</option>
          <option value="error">{{ i18n.t('pipelineSettings.error') }}</option>
        </select>
      </div>
    </div>

    <div class="setting-actions">
      <button @click="saveSettings" class="save-btn">{{ i18n.t('pipelineSettings.save') }}</button>
      <button @click="resetSettings" class="reset-btn">{{ i18n.t('pipelineSettings.reset') }}</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { inject } from 'vue'
import { pipelineDragConfig, updatePipelineDragConfig } from '../../config/pipelineDragConfig.js'

const i18n = inject('i18n')
const localConfig = reactive(JSON.parse(JSON.stringify(pipelineDragConfig)))

function saveSettings() {
  updatePipelineDragConfig(localConfig)
}

function resetSettings() {
  Object.assign(localConfig, JSON.parse(JSON.stringify(pipelineDragConfig)))
}
</script>

<style scoped>
.pipeline-drag-settings {
  padding: 16px;
  background: #0D1525;
  border-radius: 8px;
  color: #E8EEF4;
}

h3 {
  margin-top: 0;
  margin-bottom: 16px;
  color: #00D4FF;
  font-size: 16px;
  font-weight: 600;
}

h4 {
  margin-top: 14px;
  margin-bottom: 8px;
  color: #E8EEF4;
  font-size: 13px;
  border-bottom: 1px solid rgba(0, 212, 255, 0.12);
  padding-bottom: 4px;
}

.setting-section {
  margin-bottom: 12px;
}

.setting-item {
  margin-bottom: 8px;
  display: flex;
  align-items: center;
}

.setting-item label {
  width: 120px;
  font-size: 12px;
  color: #7A8FA6;
}

.setting-item input[type="number"] {
  width: 60px;
  padding: 4px 6px;
  background: #040810;
  border: 1px solid rgba(0, 212, 255, 0.2);
  color: #E8EEF4;
  border-radius: 4px;
}
.setting-item input[type="number"]:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.5);
}

.setting-item input[type="color"] {
  width: 36px;
  height: 22px;
  padding: 0;
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 4px;
  cursor: pointer;
  background: #040810;
}

.setting-item select {
  padding: 4px 6px;
  background: #040810;
  border: 1px solid rgba(0, 212, 255, 0.2);
  color: #E8EEF4;
  border-radius: 4px;
  font-size: 12px;
}
.setting-item select:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.5);
}

.setting-item.checkbox {
  display: flex;
  align-items: center;
}
.setting-item.checkbox input[type="checkbox"] {
  margin-right: 8px;
  accent-color: #00D4FF;
}
.setting-item.checkbox label {
  width: auto;
  cursor: pointer;
}

.unit {
  margin-left: 4px;
  font-size: 10px;
  color: #4A5E73;
}

.setting-actions {
  margin-top: 16px;
  display: flex;
  gap: 8px;
}

.save-btn, .reset-btn {
  padding: 7px 14px;
  border: none;
  border-radius: 4px;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.2s;
}

.save-btn {
  background: #00D4FF;
  color: #040810;
  font-weight: 600;
}
.save-btn:hover {
  background: #00AAFF;
}

.reset-btn {
  background: #0A1020;
  color: #7A8FA6;
  border: 1px solid rgba(0, 212, 255, 0.2);
}
.reset-btn:hover {
  background: rgba(0, 212, 255, 0.1);
  color: #00D4FF;
  border-color: rgba(0, 212, 255, 0.35);
}
</style>
