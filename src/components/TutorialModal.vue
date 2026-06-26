<template>
  <Teleport to="body">
    <Transition name="modal">
      <div v-if="showModal" class="tutorial-overlay" @click.self="closeModal">
        <div class="tutorial-modal" :style="modalPosition">
          <div class="tutorial-header">
            <div class="tutorial-title">
              <span>{{ i18n.t('tutorial.title') }}</span>
            </div>
            <button class="close-btn" @click="closeModal">
              <SvgIcon name="close" size="16" />
            </button>
          </div>

          <div class="progress-bar">
            <div class="progress-fill" :style="{ width: `${progress}%` }" />
          </div>
          <div class="progress-text">
            {{ currentStep + 1 }} / {{ steps.length }}
          </div>

          <div class="tutorial-content">
            <div class="step-icon">{{ currentStepData.icon }}</div>
            <h3 class="step-title">{{ currentStepData.title }}</h3>
            <p class="step-description">{{ currentStepData.description }}</p>
            <ul v-if="currentStepData.points && currentStepData.points.length" class="step-points">
              <li v-for="(point, index) in currentStepData.points" :key="index">
                <span class="point-bullet"><SvgIcon name="check" size="14" /></span>
                {{ point }}
              </li>
            </ul>
          </div>

          <div class="tutorial-footer">
            <button class="btn btn-secondary" @click="skipTutorial">
              {{ i18n.t('tutorial.skip') }}
            </button>
            <button v-if="hasPrevStep" class="btn btn-primary" @click="prevStep">
              {{ i18n.t('tutorial.prev') }}
            </button>
            <button v-if="hasNextStep" class="btn btn-primary" @click="nextStep">
              {{ i18n.t('tutorial.next') }}
            </button>
            <button v-if="isLastStep" class="btn btn-success" @click="completeTutorial">
              {{ i18n.t('tutorial.complete') }}
            </button>
          </div>
        </div>

        <div 
          v-if="currentStepData.target" 
          class="spotlight-overlay"
          :style="spotlightStyle"
        >
          <div class="spotlight-hole" :style="spotlightHoleStyle">
            <div class="spotlight-glow" />
          </div>
          <div class="spotlight-arrow" :style="arrowStyle">
            <span class="arrow-line" />
            <span class="arrow-head">{{ currentStepData.arrow || '→' }}</span>
          </div>
        </div>

        <div v-if="showTip" class="spotlight-tip" :style="tipStyle">
          {{ currentStepData.tip }}
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'
import { useRoute } from 'vue-router'
import { useI18nStore } from '../stores/i18n'
import SvgIcon from './icons/SvgIcon.vue'

const i18n = useI18nStore()
const route = useRoute()

// Throttle helper for scroll events
const throttledCalculatePositions = (() => {
  let ticking = false
  return () => {
    if (!ticking) {
      requestAnimationFrame(() => {
        calculatePositions()
        ticking = false
      })
      ticking = true
    }
  }
})()

const showModal = ref(false)
const currentStep = ref(0)
const showTip = ref(false)

const steps = computed(() => {
  const onCanvas = route.path === '/canvas'
  return [
  {
    title: i18n.t('tutorial.step1.title'),
    description: i18n.t('tutorial.step1.description'),
    points: [
      i18n.t('tutorial.step1.point1'),
      i18n.t('tutorial.step1.point2'),
      i18n.t('tutorial.step1.point3')
    ],
    target: null,
    tip: i18n.t('tutorial.step1.tip') || ''
  },
  {
    title: i18n.t('tutorial.step2.title'),
    description: onCanvas
      ? i18n.t('tutorial.step2.description') + ' (您已在此页面!)'
      : i18n.t('tutorial.step2.description'),
    points: [
      i18n.t('tutorial.step2.point1'),
      i18n.t('tutorial.step2.point2'),
      i18n.t('tutorial.step2.point3'),
      i18n.t('tutorial.step2.point4')
    ],
    target: '#canvas-toolbar',
    arrow: '↑',
    tip: i18n.t('tutorial.step2.tip') || ''
  },
  {
    title: i18n.t('tutorial.step3.title'),
    description: i18n.t('tutorial.step3.description'),
    points: [
      i18n.t('tutorial.step3.point1'),
      i18n.t('tutorial.step3.point2'),
      i18n.t('tutorial.step3.point3')
    ],
    target: 'a[href="#/items"]',
    arrow: '↑',
    tip: i18n.t('tutorial.step3.tip') || ''
  },
  {
    title: i18n.t('tutorial.step4.title'),
    description: i18n.t('tutorial.step4.description'),
    points: [
      i18n.t('tutorial.step4.point1'),
      i18n.t('tutorial.step4.point2'),
      i18n.t('tutorial.step4.point3'),
      i18n.t('tutorial.step4.point4')
    ],
    target: 'a[href="#/calculator"]',
    arrow: '↑',
    tip: i18n.t('tutorial.step4.tip') || ''
  },
  {
    title: i18n.t('tutorial.step5.title'),
    description: i18n.t('tutorial.step5.description'),
    points: [
      i18n.t('tutorial.step5.point1'),
      i18n.t('tutorial.step5.point2')
    ],
    target: null,
    tip: i18n.t('tutorial.step5.tip') || ''
  }
  ]
})

const currentStepData = computed(() => steps.value[currentStep.value])
const progress = computed(() => ((currentStep.value + 1) / steps.value.length) * 100)
const hasPrevStep = computed(() => currentStep.value > 0)
const hasNextStep = computed(() => currentStep.value < steps.value.length - 1)
const isLastStep = computed(() => currentStep.value === steps.value.length - 1)

const modalPosition = ref({})
const spotlightStyle = ref({})
const spotlightHoleStyle = ref({})
const arrowStyle = ref({})
const tipStyle = ref({})

const targetElement = ref(null)
let resizeObserver = null

const calculatePositions = () => {
  const step = currentStepData.value
  
  if (!step.target) {
    modalPosition.value = {
      top: '50%',
      left: '50%',
      transform: 'translate(-50%, -50%)'
    }
    spotlightStyle.value = { display: 'none' }
    arrowStyle.value = { display: 'none' }
    tipStyle.value = { display: 'none' }
    showTip.value = false
    return
  }

  showTip.value = !!step.tip
  const el = document.querySelector(step.target)
  
  if (!el) {
    modalPosition.value = {
      top: '50%',
      left: '50%',
      transform: 'translate(-50%, -50%)'
    }
    spotlightStyle.value = { display: 'none' }
    arrowStyle.value = { display: 'none' }
    tipStyle.value = { display: 'none' }
    return
  }

  targetElement.value = el
  const rect = el.getBoundingClientRect()
  const padding = 8
  
  const windowWidth = window.innerWidth
  const windowHeight = window.innerHeight
  const modalWidth = 550
  const modalHeight = 450

  let top, left
  const preferTop = rect.bottom + 20 < windowHeight - modalHeight
  const preferBottom = rect.top - 20 > modalHeight
  const preferLeft = rect.right + 20 < windowWidth - modalWidth
  const preferRight = rect.left - 20 > modalWidth

  const centerX = rect.left + rect.width / 2
  const centerY = rect.top + rect.height / 2

  if (centerX < windowWidth / 2) {
    left = Math.min(rect.right + padding + 200, windowWidth - modalWidth - 20)
  } else {
    left = Math.max(rect.left - padding - modalWidth - 200, 20)
  }

  if (centerY < windowHeight / 2) {
    top = Math.max(20, rect.bottom + padding + 20)
  } else {
    top = Math.min(rect.top - padding - modalHeight - 20, windowHeight - modalHeight - 20)
  }

  top = Math.max(20, Math.min(top, windowHeight - modalHeight - 20))
  left = Math.max(20, Math.min(left, windowWidth - modalWidth - 20))

  modalPosition.value = {
    position: 'fixed',
    top: `${top}px`,
    left: `${left}px`,
    transform: 'none'
  }

  const holeTop = rect.top - padding
  const holeLeft = rect.left - padding
  const holeWidth = rect.width + padding * 2
  const holeHeight = rect.height + padding * 2

  spotlightStyle.value = {
    position: 'fixed',
    top: '0',
    left: '0',
    width: '100%',
    height: '100%',
    pointerEvents: 'none'
  }

  spotlightHoleStyle.value = {
    position: 'absolute',
    top: `${holeTop}px`,
    left: `${holeLeft}px`,
    width: `${holeWidth}px`,
    height: `${holeHeight}px`,
    boxShadow: `0 0 0 9999px rgba(0, 0, 0, 0.7)`,
    clipPath: 'polygon(0 0, calc(100% - 8px) 0, 100% 8px, 100% 100%, 8px 100%, 0 calc(100% - 8px))',
    border: '3px solid #00D4FF',
    transition: 'all 0.4s ease'
  }

  const arrowX = left + modalWidth / 2
  const arrowY = top - 50
  const arrowRotation = '180deg'

  arrowStyle.value = {
    position: 'fixed',
    top: `${arrowY}px`,
    left: `${arrowX}px`,
    transform: `translateX(-50%) rotate(${arrowRotation})`,
    fontSize: '2rem',
    color: '#00D4FF',
    textShadow: '0 0 20px rgba(0, 212, 255, 0.8)',
    transition: 'all 0.4s ease'
  }

  tipStyle.value = {
    position: 'fixed',
    top: `${rect.bottom + padding + 10}px`,
    left: `${rect.left + rect.width / 2}px`,
    transform: 'translateX(-50%)',
    background: 'rgba(0, 212, 255, 0.9)',
    color: '#fff',
    padding: '8px 16px',
    clipPath: 'polygon(0 0, calc(100% - 6px) 0, 100% 6px, 100% 100%, 6px 100%, 0 calc(100% - 6px))',
    fontSize: '0.9rem',
    whiteSpace: 'nowrap',
    boxShadow: '0 4px 15px rgba(0, 212, 255, 0.4)',
    zIndex: '10001',
    transition: 'all 0.4s ease'
  }
}

const nextStep = () => {
  if (hasNextStep.value) {
    currentStep.value++
    nextTick(() => {
      calculatePositions()
    })
  }
}

const prevStep = () => {
  if (hasPrevStep.value) {
    currentStep.value--
    nextTick(() => {
      calculatePositions()
    })
  }
}

const closeModal = () => {
  showModal.value = false
  localStorage.setItem('endfield_tutorial_completed', 'true')
}

const skipTutorial = () => {
  localStorage.setItem('endfield_tutorial_completed', 'true')
  closeModal()
}

const completeTutorial = () => {
  localStorage.setItem('endfield_tutorial_completed', 'true')
  closeModal()
}

const show = () => {
  currentStep.value = 0
  showModal.value = true
  nextTick(() => {
    calculatePositions()
  })
}

const reset = () => {
  localStorage.removeItem('endfield_tutorial_completed')
  currentStep.value = 0
}

onMounted(() => {
  const hasCompleted = localStorage.getItem('endfield_tutorial_completed')
  
  if (!hasCompleted) {
    setTimeout(() => {
      show()
    }, 500)
  }

  window.resetTutorial = () => {
    reset()
    show()
    console.log('教程已重置，弹窗已显示')
  }

  resizeObserver = new ResizeObserver(() => {
    if (showModal.value) {
      calculatePositions()
    }
  })
  resizeObserver.observe(document.body)
  
  window.addEventListener('scroll', throttledCalculatePositions, true)
  window.addEventListener('resize', calculatePositions)
})

onUnmounted(() => {
  if (resizeObserver) {
    resizeObserver.disconnect()
  }
  window.removeEventListener('scroll', throttledCalculatePositions, true)
  window.removeEventListener('resize', calculatePositions)
})

defineExpose({
  show,
  reset
})
</script>

<style scoped>
.tutorial-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: flex-start;
  z-index: 9998;
  padding: 1rem;
  pointer-events: auto;
}

.tutorial-modal {
  background: linear-gradient(145deg, rgba(26, 26, 46, 0.98), rgba(45, 45, 68, 0.95));
  border: 2px solid rgba(0, 212, 255, 0.3);
  border-radius: 20px;
  width: 100%;
  max-width: 550px;
  max-height: 90vh;
  overflow: hidden;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5), 0 0 40px rgba(0, 212, 255, 0.2);
  animation: modalAppear 0.4s ease-out;
  z-index: 9999;
  position: fixed;
}

.tutorial-modal::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.6), transparent);
  z-index: 1;
  pointer-events: none;
  animation: modalScan 3s ease-in-out infinite;
}

@keyframes modalScan {
  0% { top: 0; opacity: 1; }
  50% { top: calc(100% - 2px); opacity: 0.3; }
  100% { top: 0; opacity: 1; }
}

@keyframes modalAppear {
  from {
    opacity: 0;
    transform: scale(0.9) translateY(20px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

.tutorial-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem 2rem;
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.1), rgba(0, 212, 255, 0.05));
  border-bottom: 1px solid rgba(0, 212, 255, 0.2);
}

.tutorial-title {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  color: #00D4FF;
  font-size: 1.3rem;
  font-weight: 600;
  text-shadow: 0 0 15px rgba(0, 212, 255, 0.4);
}

.tutorial-icon {
  font-size: 1.5rem;
}

.close-btn {
  background: rgba(0, 212, 255, 0.2);
  border: 1px solid rgba(0, 212, 255, 0.4);
  color: #00D4FF;
  width: 32px;
  height: 32px;
  clip-path: polygon(0 0, calc(100% - 6px) 0, 100% 6px, 100% 100%, 6px 100%, 0 calc(100% - 6px));
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  transition: all 0.3s;
}

.close-btn:hover {
  background: rgba(0, 212, 255, 0.3);
  transform: scale(1.1);
}

.progress-bar {
  height: 4px;
  background: rgba(45, 45, 68, 0.8);
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #00D4FF, #00AAFF);
  background-size: 200% 100%;
  transition: width 0.4s ease;
  box-shadow: 0 0 15px rgba(0, 212, 255, 0.5);
  animation: shimmer 2s linear infinite;
}

@keyframes shimmer {
  to {
    background-position: 200% center;
  }
}

.progress-text {
  text-align: center;
  padding: 0.75rem;
  color: #c5c5c5;
  font-size: 0.9rem;
  background: rgba(45, 45, 68, 0.4);
}

.tutorial-content {
  padding: 2rem;
  max-height: 40vh;
  overflow-y: auto;
}

.step-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
  text-align: center;
  filter: drop-shadow(0 4px 12px rgba(0, 212, 255, 0.3));
}

.step-title {
  color: #00D4FF;
  font-size: 1.4rem;
  font-weight: 600;
  margin-bottom: 1rem;
  text-align: center;
  text-shadow: 0 0 15px rgba(0, 212, 255, 0.4);
}

.step-description {
  color: #f0f0f0;
  font-size: 1rem;
  line-height: 1.6;
  margin-bottom: 1.5rem;
  text-align: center;
}

.step-points {
  list-style: none;
  padding: 0;
  margin: 0;
}

.step-points li {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 0.75rem 0;
  color: #c5c5c5;
  border-bottom: 1px solid rgba(0, 212, 255, 0.1);
}

.step-points li:last-child {
  border-bottom: none;
}

.point-bullet {
  color: #4ecdc4;
  font-weight: bold;
  flex-shrink: 0;
  font-size: 0.9rem;
}

.tutorial-footer {
  display: flex;
  gap: 0.75rem;
  padding: 1.5rem 2rem;
  background: rgba(45, 45, 68, 0.4);
  border-top: 1px solid rgba(0, 212, 255, 0.2);
  justify-content: center;
  flex-wrap: wrap;
}

.btn {
  padding: 0.75rem 1.5rem;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  border: 2px solid transparent;
  font-size: 0.95rem;
}

.btn-primary {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.3), rgba(0, 212, 255, 0.2));
  border-color: rgba(0, 212, 255, 0.5);
  color: #00D4FF;
}

.btn-primary:hover {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.5), rgba(0, 212, 255, 0.3));
  border-color: rgba(0, 212, 255, 0.8);
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(0, 212, 255, 0.3);
}

.btn-secondary {
  background: rgba(45, 45, 68, 0.6);
  border-color: rgba(255, 255, 255, 0.2);
  color: #c5c5c5;
}

.btn-secondary:hover {
  background: rgba(55, 55, 78, 0.7);
  border-color: rgba(255, 255, 255, 0.3);
  color: #f0f0f0;
}

.btn-success {
  background: linear-gradient(135deg, rgba(78, 205, 196, 0.3), rgba(78, 205, 196, 0.2));
  border-color: rgba(78, 205, 196, 0.5);
  color: #4ecdc4;
}

.btn-success:hover {
  background: linear-gradient(135deg, rgba(78, 205, 196, 0.5), rgba(78, 205, 196, 0.3));
  border-color: rgba(78, 205, 196, 0.8);
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(78, 205, 196, 0.3);
}

.spotlight-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 9997;
}

.spotlight-hole {
  position: absolute;
  box-shadow: 0 0 0 9999px rgba(0, 0, 0, 0.75);
  clip-path: polygon(0 0, calc(100% - 8px) 0, 100% 8px, 100% 100%, 8px 100%, 0 calc(100% - 8px));
  border: 3px solid #00D4FF;
  transition: all 0.4s ease;
  animation: pulseGlow 2s ease-in-out infinite;
}

@keyframes pulseGlow {
  0%, 100% {
    box-shadow: 0 0 0 9999px rgba(0, 0, 0, 0.7), 0 0 20px rgba(0, 212, 255, 0.5);
  }
  50% {
    box-shadow: 0 0 0 9999px rgba(0, 0, 0, 0.7), 0 0 40px rgba(0, 212, 255, 0.8);
  }
}

.spotlight-glow {
  position: absolute;
  top: -3px;
  left: -3px;
  right: -3px;
  bottom: -3px;
  border-radius: 14px;
  background: transparent;
  animation: glowPulse 1.5s ease-in-out infinite;
}

@keyframes glowPulse {
  0%, 100% {
    box-shadow: inset 0 0 10px rgba(0, 212, 255, 0.3);
  }
  50% {
    box-shadow: inset 0 0 20px rgba(0, 212, 255, 0.6);
  }
}

.spotlight-arrow {
  position: fixed;
  color: #00D4FF;
  font-size: 2rem;
  text-shadow: 0 0 20px rgba(0, 212, 255, 0.8);
  animation: bounce 1s ease-in-out infinite;
  z-index: 10000;
  pointer-events: none;
}

@keyframes bounce {
  0%, 100% {
    transform: translateX(-50%) translateY(0);
  }
  50% {
    transform: translateX(-50%) translateY(-10px);
  }
}

.arrow-line {
  display: block;
  width: 3px;
  height: 30px;
  background: linear-gradient(to bottom, #00D4FF, transparent);
  margin: 0 auto;
  border-radius: 2px;
}

.arrow-head {
  display: block;
  text-align: center;
  font-size: 1.5rem;
  margin-top: -5px;
}

.spotlight-tip {
  position: fixed;
  background: rgba(0, 212, 255, 0.95);
  color: #fff;
  padding: 10px 20px;
  border-radius: 10px;
  font-size: 0.95rem;
  font-weight: 500;
  box-shadow: 0 8px 30px rgba(0, 212, 255, 0.5);
  z-index: 10002;
  animation: tipAppear 0.4s ease-out;
  pointer-events: none;
}

.spotlight-tip::before {
  content: '';
  position: absolute;
  top: -8px;
  left: 50%;
  transform: translateX(-50%);
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  border-bottom: 8px solid rgba(0, 212, 255, 0.95);
}

@keyframes tipAppear {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .tutorial-modal,
.modal-leave-active .tutorial-modal {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.modal-enter-from .tutorial-modal,
.modal-leave-to .tutorial-modal {
  opacity: 0;
  transform: scale(0.9) translateY(20px);
}

@media (max-width: 768px) {
  .tutorial-modal {
    max-width: 95%;
    max-height: 95vh;
  }

  .tutorial-header {
    padding: 1rem 1.5rem;
  }

  .tutorial-title {
    font-size: 1.1rem;
  }

  .tutorial-content {
    padding: 1.5rem;
    max-height: 50vh;
  }

  .step-icon {
    font-size: 2.5rem;
  }

  .step-title {
    font-size: 1.2rem;
  }

  .tutorial-footer {
    padding: 1rem 1.5rem;
  }

  .btn {
    padding: 0.6rem 1.2rem;
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .tutorial-overlay {
    padding: 0.5rem;
  }

  .tutorial-content {
    padding: 1rem;
  }

  .step-icon {
    font-size: 2rem;
  }

  .step-description {
    font-size: 0.9rem;
  }

  .step-points li {
    font-size: 0.9rem;
    padding: 0.5rem 0;
  }
}
</style>