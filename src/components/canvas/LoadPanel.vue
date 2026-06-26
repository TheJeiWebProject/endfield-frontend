<template>
  <div
    class="modal-overlay"
    @click.self="$emit('close')"
  >
    <div class="modal-content">
      <div class="modal-header">
        <h3>{{ i18n.t('canvas.loadPanel.title') }}</h3>
        <button
          class="close-btn"
          @click="$emit('close')"
        >
          <SvgIcon name="close" size="18" />
        </button>
      </div>

      <div class="modal-body">
        <div class="import-section">
          <h4>{{ i18n.t('canvas.loadPanel.import') }}</h4>
          <input
            ref="fileInput"
            type="file"
            accept=".json"
            style="display: none"
            @change="handleFileChange"
          >
          <button
            class="btn secondary"
            @click="$refs.fileInput.click()"
          >
            {{ i18n.t('canvas.loadPanel.selectFile') }}
          </button>
        </div>

        <div class="layouts-list">
          <h4>{{ i18n.t('canvas.loadPanel.saved') }}</h4>
          <div
            v-for="layout in layouts"
            :key="layout.id"
            class="layout-item"
            :class="{ selected: selectedLayout?.id === layout.id }"
            @click="selectedLayout = layout"
          >
            <div class="layout-info">
              <h5>{{ layout.name }}</h5>
              <p>{{ new Date(layout.timestamp).toLocaleString() }}</p>
              <div class="layout-meta">
                <span>{{ i18n.t('blueprints.panel.machines', { count: layout.machineCount || layout.machines?.length || 0 }) }}</span>
                <span>{{ i18n.t('canvas.loadPanel.grid', { cols: layout.gridCols, rows: layout.gridRows }) }}</span>
              </div>
            </div>
            <div class="layout-actions">
              <button
                class="btn primary"
                @click.stop="$emit('load', layout)"
              >
                {{ i18n.t('canvas.loadPanel.load') }}
              </button>
              <button
                class="btn danger"
                @click.stop="$emit('delete', layout)"
              >
                {{ i18n.t('canvas.loadPanel.delete') }}
              </button>
            </div>
          </div>
          <div
            v-if="layouts.length === 0"
            class="empty-state"
          >
            <p>{{ i18n.t('canvas.loadPanel.empty') }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref , inject} from 'vue'
import SvgIcon from '../icons/SvgIcon.vue'

defineProps({
  layouts: { type: Array, default: () => [] }
})

const emit = defineEmits(['load', 'delete', 'import', 'close'])
const i18n = inject('i18n')

const selectedLayout = ref(null)

function handleFileChange(e) {
  const file = e.target.files[0]
  if (file) {
    emit('import', file)
  }
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
  width: 500px;
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

.search-box {
  margin-bottom: 12px;
}
.search-box input {
  width: 100%;
  padding: 7px 10px;
  border: 1px solid rgba(0, 212, 255, 0.18);
  border-radius: 4px;
  background: #040810;
  color: #E8EEF4;
  font-size: 13px;
}
.search-box input:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.4);
}
.search-box input::placeholder {
  color: #4A5E73;
}

.layout-list {
  display: flex;
  flex-direction: column;
  gap: 6px;
  margin-bottom: 16px;
}

.layout-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 12px;
  background: #0A1020;
  border: 1px solid rgba(0, 212, 255, 0.1);
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
}
.layout-item:hover {
  background: rgba(0, 212, 255, 0.05);
  border-color: rgba(0, 212, 255, 0.2);
}
.layout-item.selected {
  background: rgba(0, 212, 255, 0.1);
  border-color: rgba(0, 212, 255, 0.35);
}

.layout-info h5 {
  margin: 0 0 3px 0;
  color: #E8EEF4;
  font-size: 13px;
}
.layout-info p {
  margin: 0;
  color: #7A8FA6;
  font-size: 11px;
}

.layout-actions {
  display: flex;
  gap: 4px;
}

.empty-state {
  text-align: center;
  padding: 24px;
  color: #4A5E73;
  font-size: 13px;
}

.import-section {
  margin-top: 16px;
  padding-top: 16px;
  border-top: 1px solid rgba(0, 212, 255, 0.08);
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
.btn.primary {
  background: rgba(0, 212, 255, 0.12);
  border: 1px solid rgba(0, 212, 255, 0.25);
  color: #00D4FF;
}
.btn.primary:hover {
  background: rgba(0, 212, 255, 0.25);
}
.btn.danger {
  background: transparent;
  border: 1px solid rgba(255, 68, 102, 0.15);
  color: #7A8FA6;
  font-size: 11px;
  padding: 4px 8px;
}
.btn.danger:hover {
  background: rgba(255, 68, 102, 0.12);
  color: #FF4466;
  border-color: rgba(255, 68, 102, 0.3);
}

.modal-body::-webkit-scrollbar { width: 4px; }
.modal-body::-webkit-scrollbar-track { background: transparent; }
.modal-body::-webkit-scrollbar-thumb { background: rgba(0, 212, 255, 0.2); border-radius: 2px; }
</style>