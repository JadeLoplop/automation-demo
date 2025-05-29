<template>
    <div class="mecha-container">
      <canvas ref="canvasRef" class="three-canvas"></canvas>
      <div class="overlay">
        <h1>Automated Mecha Systems</h1>
        <p>Deploy, test, and evolve with cybernetic precision.</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import * as THREE from 'three'
  import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
  
  const canvasRef = ref(null)
  
  onMounted(() => {
    const canvas = canvasRef.value
    const scene = new THREE.Scene()
    scene.background = new THREE.Color('#0a0a1f')
  
    const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100)
    camera.position.set(0, 2, 6)
  
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true })
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.setPixelRatio(window.devicePixelRatio)
  
    const ambient = new THREE.AmbientLight(0xffffff, 0.6)
    const point = new THREE.PointLight(0x00ffff, 1.5)
    point.position.set(0, 3, 5)
    scene.add(ambient, point)
  
    const controls = new OrbitControls(camera, renderer.domElement)
    controls.enableDamping = true
    controls.autoRotate = true
  
    const clock = new THREE.Clock()
    let mixer = null
  
    const loader = new GLTFLoader()
    loader.load('/models/mech.glb', (gltf) => {
      const model = gltf.scene
      model.scale.set(1, 1, 1)
      model.position.set(0, -1, 0)
      scene.add(model)
  
      if (gltf.animations.length) {
        mixer = new THREE.AnimationMixer(model)
        gltf.animations.forEach((clip) => {
          mixer.clipAction(clip).play()
        })
      }
    })
  
    const animate = () => {
      requestAnimationFrame(animate)
      const delta = clock.getDelta()
      if (mixer) mixer.update(delta)
      controls.update()
      renderer.render(scene, camera)
    }
  
    animate()
  })
  </script>
  
  
  <style scoped>
  .mecha-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    z-index: 0;
  }
  
  .three-canvas {
    width: 100%;
    height: 100%;
    display: block;
  }
  
  .overlay {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    z-index: 10;
    pointer-events: none;
    color: white;
  }
  
  .overlay h1 {
    font-size: 7vw;
    margin-bottom: 1rem;
    background: linear-gradient(90deg, #00ffff, #ff00ff);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  
  .overlay p {
    font-size: 4vw;
    color: #ccc;
  }
  
  @media (min-width: 768px) {
    .overlay h1 {
      font-size: 3rem;
    }
  
    .overlay p {
      font-size: 1.25rem;
    }
  }
  </style>
  