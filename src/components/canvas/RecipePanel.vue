<template>
  <div
    class="modal-overlay"
    @click.self="$emit('close')"
  >
    <div class="modal-content">
      <div class="modal-header">
        <h3>{{ i18n.t('canvas.recipePanel.title') }}</h3>
        <button
          class="close-btn"
          @click="$emit('close')"
        >
          <SvgIcon name="close" size="18" />
        </button>
      </div>

      <div class="modal-body">
        <div
          v-if="selectedItem"
          class="item-section"
        >
          <div class="item-header">
            <img
              :src="dataStore.getIconUrl(selectedItem.icon)"
              :alt="selectedItem.name"
            >
            <h4>{{ selectedItem.name }}</h4>
          </div>

          <div
            v-if="selectedItemRecipe"
            class="recipe-details"
          >
            <div class="recipe-section">
              <h5>{{ i18n.t('canvas.recipePanel.input') }}</h5>
              <div class="recipe-items">
                <div
                  v-for="(amount, itemId) in selectedItemRecipe.in"
                  :key="itemId"
                  class="recipe-item"
                >
                  <img
                    :src="dataStore.getIconUrl(getItemIcon(itemId))"
                    :alt="getItemName(itemId)"
                  >
                  <span>{{ getItemName(itemId) }}</span>
                  <span class="amount">×{{ amount }}</span>
                </div>
              </div>
            </div>

            <div class="recipe-arrow">
              <SvgIcon name="arrowRight" size="16" />
            </div>

            <div class="recipe-section">
              <h5>{{ i18n.t('canvas.recipePanel.output') }}</h5>
              <div class="recipe-items">
                <div
                  v-for="(amount, itemId) in selectedItemRecipe.out"
                  :key="itemId"
                  class="recipe-item"
                >
                  <img
                    :src="dataStore.getIconUrl(getItemIcon(itemId))"
                    :alt="getItemName(itemId)"
                  >
                  <span>{{ getItemName(itemId) }}</span>
                  <span class="amount">×{{ amount }}</span>
                </div>
              </div>
            </div>

            <div class="recipe-info">
              <div class="info-row">
                <span>{{ i18n.t('canvas.recipePanel.time') }}: {{ selectedItemRecipe.time }}{{ i18n.t('unit.second') }}</span>
              </div>
              <div class="info-row">
                <span>{{ i18n.t('canvas.recipePanel.power') }}</span>
                <span>{{ selectedItem.machine?.power || 0 }}kW</span>
              </div>
            </div>
          </div>

          <div
            v-else
            class="no-recipe"
          >
            <p>{{ i18n.t('canvas.recipePanel.noRecipe') }}</p>
          </div>
        </div>

        <div
          v-else
          class="no-selection"
        >
          <p>{{ i18n.t('canvas.recipePanel.selectDevice') }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { inject } from 'vue'
import { useDataStore } from '../../stores/data.js'
import SvgIcon from '../icons/SvgIcon.vue'

const dataStore = useDataStore()

defineProps({
  selectedItem: { type: Object, default: null },
  selectedItemRecipe: { type: Object, default: null }
})
const i18n = inject('i18n')

defineEmits(['close'])

function getItemName(itemId) {
  const item = dataStore.getItemById(itemId)
  return item?.name || i18n.t('canvas.rightSidebar.unknownItem')
}

function getItemIcon(itemId) {
  const item = dataStore.getItemById(itemId)
  return item?.icon || ''
}
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

.modal-body h5 {
  color: #00D4FF;
  font-size: 12px;
  font-weight: 600;
  margin: 0 0 8px 0;
}

.recipe-detail-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 8px;
  margin-bottom: 4px;
  background: #0A1020;
  border-radius: 4px;
  border: 1px solid rgba(0, 212, 255, 0.08);
}

.recipe-detail-item img {
  width: 24px; height: 24px;
  object-fit: contain;
}

.recipe-detail-item span {
  flex: 1;
  color: #E8EEF4;
  font-size: 12px;
}

.recipe-detail-item .amount {
  color: #00D4FF;
  font-family: monospace;
  font-size: 11px;
}

.recipe-meta {
  display: flex;
  gap: 16px;
  margin-top: 12px;
  font-size: 11px;
  color: #7A8FA6;
}

.empty-info {
  text-align: center;
  padding: 24px;
  color: #4A5E73;
  font-size: 13px;
}

.modal-body::-webkit-scrollbar { width: 4px; }
.modal-body::-webkit-scrollbar-track { background: transparent; }
.modal-body::-webkit-scrollbar-thumb { background: rgba(0, 212, 255, 0.2); border-radius: 2px; }
</style>