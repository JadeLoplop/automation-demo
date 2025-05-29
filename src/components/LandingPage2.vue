<template>
    <div class="container">
      <canvas ref="canvas" class="canvas"></canvas>
      <div class="overlay-text">
        <h5>Automation should be breezy!!</h5>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onUnmounted } from 'vue'
  import * as THREE from 'three'
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
  import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
  
  const canvas = ref(null)
  
  onMounted(() => {
    const scene = new THREE.Scene()
  
    // Gradient background colors
    const skyColorTop = new THREE.Color('#87CEEB') // light blue
    const skyColorBottom = new THREE.Color('#FFD580') // warm sandy yellow
  
    // Camera setup
    const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100)
    camera.position.set(0, 1.5, 4)
  
    // Renderer setup
    const renderer = new THREE.WebGLRenderer({ canvas: canvas.value, antialias: true, alpha: true })
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.setClearColor(0x000000, 0) // transparent background for gradient
  
    // Gradient background plane
    const bgGeometry = new THREE.PlaneGeometry(20, 20)
    const bgMaterial = new THREE.ShaderMaterial({
      uniforms: {
        topColor: { value: skyColorTop },
        bottomColor: { value: skyColorBottom }
      },
      vertexShader: `
        varying vec2 vUv;
        void main() {
          vUv = uv;
          gl_Position = projectionMatrix * modelViewMatrix * vec4(position,1.0);
        }
      `,
      fragmentShader: `
        uniform vec3 topColor;
        uniform vec3 bottomColor;
        varying vec2 vUv;
        void main() {
          gl_FragColor = vec4(mix(bottomColor, topColor, vUv.y), 1.0);
        }
      `,
      side: THREE.DoubleSide,
      depthWrite: false
    })
    const bgMesh = new THREE.Mesh(bgGeometry, bgMaterial)
    bgMesh.position.z = -10
    scene.add(bgMesh)
  
    // Lights
    const ambient = new THREE.AmbientLight(0xffffff, 0.6)
    const directional = new THREE.DirectionalLight(0xffddaa, 1)
    directional.position.set(5, 5, 5)
    scene.add(ambient, directional)
  
    // Controls
    const controls = new OrbitControls(camera, renderer.domElement)
    controls.enableDamping = true
    controls.enableZoom = false
    controls.autoRotate = true
    controls.autoRotateSpeed = 0.4
  
    // Animation mixer for model animations
    const clock = new THREE.Clock()
    let mixer = null
  
    // Load your GLB model here
    const loader = new GLTFLoader()
    loader.load('/models/smol.glb',
      (gltf) => {
        const model = gltf.scene
        model.position.set(0, -1, 0)
        model.scale.set(1, 1, 1)
        scene.add(model)
  
        // Setup animation mixer if animations exist
        if (gltf.animations && gltf.animations.length) {
          mixer = new THREE.AnimationMixer(model)
          gltf.animations.forEach((clip) => {
            mixer.clipAction(clip).play()
          })
        }
      },
      undefined,
      (error) => {
        console.error('Error loading model:', error)
      }
    )
  
    // Handle window resizing
    function onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight
      camera.updateProjectionMatrix()
      renderer.setSize(window.innerWidth, window.innerHeight)
    }
    window.addEventListener('resize', onWindowResize)
  
    // Animation loop
    function animate() {
      requestAnimationFrame(animate)
      const delta = clock.getDelta()
      if (mixer) mixer.update(delta)
      controls.update()
      renderer.render(scene, camera)
    }
    animate()
  
    onUnmounted(() => {
      window.removeEventListener('resize', onWindowResize)
      renderer.dispose()
    })
  })
  </script>
  
  <style scoped>
  .container {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    background: linear-gradient(to bottom, #87CEEB 0%, #FFD580 100%);
  }
  
  .canvas {
    display: block;
    width: 100%;
    height: 100%;
  }
  
  .overlay-text {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    color: #003300;
    text-shadow: 0 0 8px rgba(255, 255, 255, 0.8);
    user-select: none;
    pointer-events: none;
    text-align: center;
    font-weight: 700;
    font-size: clamp(2rem, 6vw, 5rem);
    padding: 0 1rem;
  }
  </style>
  