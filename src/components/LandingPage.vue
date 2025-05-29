<template>
    <div class="mecha-container">
      <canvas ref="canvasRef" class="three-canvas"></canvas>
  
      <!-- Sketchfab embed mech iframe -->
      <div class="sketchfab-embed-wrapper">
        <iframe
          title="Mecha Game Project"
          frameborder="0"
          allowfullscreen
          mozallowfullscreen="true"
          webkitallowfullscreen="true"
          allow="autoplay; fullscreen; xr-spatial-tracking"
          xr-spatial-tracking
          execution-while-out-of-viewport
          execution-while-not-rendered
          web-share
          src="https://sketchfab.com/models/1715d53a409643ebba477b5f25573804/embed"
        ></iframe>
        <p style="font-size: 13px; font-weight: normal; margin: 5px; color: #4A4A4A;">
          <a
            href="https://sketchfab.com/3d-models/mecha-game-project-1715d53a409643ebba477b5f25573804"
            target="_blank"
            rel="nofollow"
            style="font-weight: bold; color: #1CAAD9;"
            >Mecha Game Project</a
          >
          by
          <a
            href="https://sketchfab.com/polyflow"
            target="_blank"
            rel="nofollow"
            style="font-weight: bold; color: #1CAAD9;"
            >polyflow</a
          >
          on
          <a
            href="https://sketchfab.com"
            target="_blank"
            rel="nofollow"
            style="font-weight: bold; color: #1CAAD9;"
            >Sketchfab</a
          >
        </p>
      </div>
  
      <div class="overlay">
        <h1>Automated Mecha Systems</h1>
        <p>Deploy, test, and evolve with cybernetic precision.</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import * as THREE from 'three'
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
  
    // No GLB loader here anymore
  
    const animate = () => {
      requestAnimationFrame(animate)
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
    background: #0a0a1f;
  }
  
  .three-canvas {
    width: 100%;
    height: 100%;
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
  }
  
  /* Make iframe fill and sit on top of the canvas */
  .sketchfab-embed-wrapper {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 80vw;
    height: 60vh;
    max-width: 900px;
    max-height: 600px;
    transform: translate(-50%, -50%);
    z-index: 1;
    pointer-events: auto;
  }
  
  .sketchfab-embed-wrapper iframe {
    width: 100%;
    height: 100%;
    border: none;
    border-radius: 10px;
    box-shadow: 0 0 20px #00ffffaa;
  }
  
  /* Overlay text stays on top */
  .overlay {
    position: absolute;
    top: 10%;
    left: 50%;
    transform: translateX(-50%);
    text-align: center;
    z-index: 10;
    pointer-events: none;
    color: white;
    user-select: none;
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
  