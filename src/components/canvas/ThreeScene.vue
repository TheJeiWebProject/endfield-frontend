<template>
  <div ref="container" class="three-scene"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'

let THREE = null
let OrbitControls = null

const loadThreeJS = async () => {
  if (THREE) return
  
  const threeModule = await import('three')
  THREE = threeModule
  
  const controlsModule = await import('three/examples/jsm/controls/OrbitControls.js')
  OrbitControls = controlsModule.OrbitControls
}

const props = defineProps({
  visible: {
    type: Boolean,
    default: true
  },
  gridData: {
    type: Object,
    default: () => ({})
  },
  activeLayer: {
    type: String,
    default: 'all'
  }
})

const emit = defineEmits(['scene-ready'])

const container = ref(null)
let scene = null
let camera = null
let renderer = null
let controls = null
let animationId = null
const objects = []
const gridHelper = null

const initScene = async () => {
  if (!container.value) return

  await loadThreeJS()

  scene = new THREE.Scene()
  scene.background = new THREE.Color(0x1a1a2e)

  const width = container.value.clientWidth
  const height = container.value.clientHeight

  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000)
  camera.position.set(10, 10, 10)
  camera.lookAt(0, 0, 0)

  renderer = new THREE.WebGLRenderer({ antialias: true })
  renderer.setSize(width, height)
  renderer.shadowMap.enabled = true
  renderer.shadowMap.type = THREE.PCFSoftShadowMap
  container.value.appendChild(renderer.domElement)

  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
  controls.screenSpacePanning = false
  controls.minDistance = 5
  controls.maxDistance = 50

  const ambientLight = new THREE.AmbientLight(0xffffff, 0.6)
  scene.add(ambientLight)

  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
  directionalLight.position.set(10, 10, 5)
  directionalLight.castShadow = true
  directionalLight.shadow.mapSize.width = 2048
  directionalLight.shadow.mapSize.height = 2048
  scene.add(directionalLight)

  const gridHelper = new THREE.GridHelper(20, 20, 0x444444, 0x222222)
  scene.add(gridHelper)

  const planeGeometry = new THREE.PlaneGeometry(20, 20)
  const planeMaterial = new THREE.MeshStandardMaterial({ 
    color: 0x0f0f1a,
    roughness: 0.8,
    metalness: 0.2
  })
  const plane = new THREE.Mesh(planeGeometry, planeMaterial)
  plane.rotation.x = -Math.PI / 2
  plane.position.y = -0.01
  plane.receiveShadow = true
  scene.add(plane)

  emit('scene-ready', { scene, camera, renderer })

  animate()
}

const createCube = (x, y, z, width, height, depth, color, metadata = {}) => {
  const geometry = new THREE.BoxGeometry(width, height, depth)
  const material = new THREE.MeshStandardMaterial({ 
    color,
    roughness: 0.7,
    metalness: 0.3
  })
  const cube = new THREE.Mesh(geometry, material)
  cube.position.set(x, y + height / 2, z)
  cube.castShadow = true
  cube.receiveShadow = true
  cube.userData = metadata
  scene.add(cube)
  objects.push(cube)
  return cube
}

const clearObjects = () => {
  objects.forEach(obj => {
    scene.remove(obj)
    if (obj.geometry) obj.geometry.dispose()
    if (obj.material) {
      if (Array.isArray(obj.material)) {
        obj.material.forEach(m => m.dispose())
      } else {
        obj.material.dispose()
      }
    }
  })
  objects.length = 0
}

const renderGridData = (data) => {
  clearObjects()
  
  if (!data || !data.grids) return

  const gridSize = 0.9
  const gap = 0.05
  
  data.grids.forEach(grid => {
    if (grid.content && grid.content.id) {
      const x = (grid.x - data.sizeX / 2) * (gridSize + gap) + gridSize / 2
      const z = (grid.y - data.sizeY / 2) * (gridSize + gap) + gridSize / 2
      
      let color = 0x4a90d9
      if (grid.content.category === 'producer') color = 0xe67e22
      else if (grid.content.category === 'processor') color = 0x2ecc71
      else if (grid.content.category === 'logistics') color = 0x9b59b6
      else if (grid.content.category === 'storage') color = 0x3498db
      
      createCube(x, 0, z, gridSize, gridSize, gridSize, color, {
        id: grid.content.id,
        name: grid.content.name,
        type: grid.content.type,
        gridX: grid.x,
        gridY: grid.y
      })
    }
  })
}

const animate = () => {
  animationId = requestAnimationFrame(animate)
  
  if (controls) {
    controls.update()
  }
  
  if (renderer && scene && camera) {
    renderer.render(scene, camera)
  }
}

const handleResize = () => {
  if (!container.value || !camera || !renderer) return

  const width = container.value.clientWidth
  const height = container.value.clientHeight

  camera.aspect = width / height
  camera.updateProjectionMatrix()
  renderer.setSize(width, height)
}

watch(() => props.visible, (newVal) => {
  if (newVal && container.value) {
    nextTick(() => {
      handleResize()
    })
  }
})

watch(() => props.gridData, (newData) => {
  if (newData && Object.keys(newData).length > 0) {
    renderGridData(newData)
  }
}, { deep: true })

onMounted(() => {
  initScene()
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
  window.removeEventListener('resize', handleResize)
  if (renderer) {
    renderer.dispose()
    if (container.value && renderer.domElement.parentNode === container.value) {
      container.value.removeChild(renderer.domElement)
    }
  }
  objects.forEach(obj => {
    if (obj.geometry) obj.geometry.dispose()
    if (obj.material) {
      if (Array.isArray(obj.material)) {
        obj.material.forEach(m => m.dispose())
      } else {
        obj.material.dispose()
      }
    }
  })
})

defineExpose({
  clearObjects,
  createCube,
  renderGridData,
  getScene: () => scene,
  getCamera: () => camera,
  getRenderer: () => renderer
})
</script>

<style scoped>
.three-scene {
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
