<template>
  <div
    class="modal-overlay"
    @click.self="$emit('close')"
  >
    <div class="modal-content">
      <div class="modal-header">
        <h3><SvgIcon name="ruler" size="16" class="panel-icon" /> {{ i18n.t('blueprints.panel.title') }}</h3>
        <button
          class="close-btn"
          @click="$emit('close')"
        >
          <SvgIcon name="close" size="18" />
        </button>
      </div>

      <div class="modal-body">
        <div class="create-section">
          <h4>{{ i18n.t('blueprints.panel.create') }}</h4>
          <input
            v-model="newBlueprintName"
            type="text"
            :placeholder="i18n.t('blueprints.panel.name')"
          >
          <textarea
            v-model="newBlueprintDesc"
            :placeholder="i18n.t('blueprints.panel.desc')"
            rows="2"
          />
          <button
            class="btn primary"
            @click="createBlueprint"
          >
            {{ i18n.t('blueprints.panel.create') }}
          </button>
        </div>

        <div class="blueprint-list">
          <h4>{{ i18n.t('blueprints.panel.existing') }}</h4>
          <div
            v-for="blueprint in blueprints"
            :key="blueprint.id"
            class="blueprint-item"
          >
            <div class="blueprint-info">
              <h5>{{ blueprint.name }}</h5>
              <p>{{ blueprint.description }}</p>
              <div class="blueprint-meta">
                <span>{{ i18n.t('blueprints.panel.machines', { count: blueprint.machineCount }) }}</span>
                <span>{{ i18n.t('blueprints.panel.power', { power: blueprint.power }) }}</span>
              </div>
            </div>
            <button
              class="btn danger"
              @click="$emit('delete', blueprint)"
            >
              {{ i18n.t('blueprints.panel.delete') }}
            </button>
          </div>
          <div
            v-if="blueprints.length === 0"
            class="empty-state"
          >
            <p>{{ i18n.t('blueprints.panel.empty') }}</p>
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
  blueprints: { type: Array, default: () => [] }
})

const emit = defineEmits(['create', 'delete', 'close'])
const i18n = inject('i18n')

const newBlueprintName = ref('')
const newBlueprintDesc = ref('')

function createBlueprint() {
  if (!newBlueprintName.value.trim()) {
    alert(i18n.t('blueprints.panel.nameRequired'))
    return
  }
  emit('create', newBlueprintName.value.trim(), newBlueprintDesc.value.trim())
  newBlueprintName.value = ''
  newBlueprintDesc.value = ''
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
  width: 460px;
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
  display: flex;
  align-items: center;
  gap: 8px;
}
.panel-icon {
  opacity: 0.7;
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

.create-section {
  margin-bottom: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid rgba(0, 212, 255, 0.08);
}

.create-section h4 {
  margin: 0 0 10px 0;
  color: #00D4FF;
  font-size: 13px;
  font-weight: 600;
}

.create-section input,
.create-section textarea {
  width: 100%;
  padding: 7px 10px;
  margin-bottom: 8px;
  border: 1px solid rgba(0, 212, 255, 0.18);
  border-radius: 4px;
  background: #040810;
  color: #E8EEF4;
  font-family: inherit;
  font-size: 13px;
}
.create-section input:focus,
.create-section textarea:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.4);
}
.create-section textarea {
  resize: vertical;
  min-height: 50px;
}

.blueprint-list h4 {
  margin: 0 0 10px 0;
  color: #00D4FF;
  font-size: 13px;
  font-weight: 600;
}

.blueprint-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 12px;
  background: #0A1020;
  border: 1px solid rgba(0, 212, 255, 0.1);
  border-radius: 6px;
  margin-bottom: 6px;
  transition: all 0.2s;
}
.blueprint-item:hover {
  background: rgba(0, 212, 255, 0.05);
  border-color: rgba(0, 212, 255, 0.2);
}

.blueprint-info {
  flex: 1;
}

.blueprint-info h5 {
  margin: 0 0 3px 0;
  color: #E8EEF4;
  font-size: 13px;
}

.blueprint-info p {
  margin: 0 0 6px 0;
  color: #7A8FA6;
  font-size: 11px;
}

.blueprint-stats {
  display: flex;
  gap: 10px;
  font-size: 10px;
  color: #4A5E73;
}

.btn {
  padding: 6px 12px;
  border: 1px solid rgba(0, 212, 255, 0.2);
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  transition: all 0.2s;
}
.btn-primary {
  background: rgba(0, 212, 255, 0.1);
  color: #00D4FF;
  width: 100%;
  margin-top: 4px;
}
.btn-primary:hover {
  background: rgba(0, 212, 255, 0.2);
}
.btn-danger {
  background: transparent;
  color: #7A8FA6;
  font-size: 11px;
}
.btn-danger:hover {
  background: rgba(255, 68, 102, 0.15);
  color: #FF4466;
  border-color: rgba(255, 68, 102, 0.3);
}
</style>
