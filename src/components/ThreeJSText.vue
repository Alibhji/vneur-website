<template>
  <div ref="container" class="three-container"></div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue'
import * as THREE from 'three'
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js'
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry.js'
import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer.js'
import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass.js'
import { UnrealBloomPass } from 'three/examples/jsm/postprocessing/UnrealBloomPass.js'

export default {
  name: 'ThreeJSText',
  setup() {
    const container = ref(null)
    let scene, camera, renderer, textMesh, neuralNetwork, composer
    let animationId
    let neuralNodes = []
    let neuralConnections = []
    let nodeInitialPositions = []
    let time = 0

    const createNeuralNetwork = () => {
      neuralNetwork = new THREE.Group()
      
      // Create neural network nodes
      const nodeCount = 30
      const nodeGeometry = new THREE.SphereGeometry(0.05, 8, 8)
      const nodeMaterial = new THREE.MeshStandardMaterial({ 
        color: 0x00ff88,
        emissive: 0x00ff88,
        emissiveIntensity: 0.6,
        metalness: 0.0,
        roughness: 0.0,
        transparent: true,
        opacity: 0.7
      })

      for (let i = 0; i < nodeCount; i++) {
        const node = new THREE.Mesh(nodeGeometry, nodeMaterial.clone())
        const initialPos = new THREE.Vector3(
          (Math.random() - 0.5) * 8,
          (Math.random() - 0.5) * 6,
          (Math.random() - 0.5) * 4 - 2
        )
        node.position.copy(initialPos)
        nodeInitialPositions.push(initialPos)
        neuralNodes.push(node)
        neuralNetwork.add(node)
      }

      // Create connections between nodes
      const connectionMaterial = new THREE.LineBasicMaterial({
        color: 0x00ff88,
        emissive: 0x00ff88,
        emissiveIntensity: 0.3,
        transparent: true,
        opacity: 0.3
      })

      for (let i = 0; i < nodeCount; i++) {
        for (let j = i + 1; j < nodeCount; j++) {
          if (Math.random() > 0.85) { // Connect some nodes
            const connectionGeometry = new THREE.BufferGeometry().setFromPoints([
              neuralNodes[i].position.clone(),
              neuralNodes[j].position.clone()
            ])
            const connection = new THREE.Line(connectionGeometry, connectionMaterial.clone())
            connection.userData = { node1: i, node2: j }
            neuralConnections.push(connection)
            neuralNetwork.add(connection)
          }
        }
      }

      scene.add(neuralNetwork)
    }


    const initThreeJS = () => {
      // Scene setup
      scene = new THREE.Scene()
      scene.background = new THREE.Color(0x000000)

      // Camera setup
      camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      )
      camera.position.z = 5

      // Renderer setup
      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
      renderer.setSize(window.innerWidth, window.innerHeight)
      renderer.setPixelRatio(window.devicePixelRatio)
      container.value.appendChild(renderer.domElement)

      // Post-processing setup with Bloom
      composer = new EffectComposer(renderer)
      composer.setSize(window.innerWidth, window.innerHeight)
      composer.setPixelRatio(window.devicePixelRatio)

      const renderPass = new RenderPass(scene, camera)
      composer.addPass(renderPass)

      // Bloom effect - matching template parameters
      const bloomPass = new UnrealBloomPass(
        new THREE.Vector2(window.innerWidth, window.innerHeight),
        1.0, // strength - more subtle
        0.5, // radius - wider glow
        0.9 // threshold - only very bright parts glow
      )
      composer.addPass(bloomPass)

      // Lighting - more subtle like template
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.2)
      scene.add(ambientLight)

      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5)
      directionalLight.position.set(1, 1, 1)
      scene.add(directionalLight)

      // Create neural network
      createNeuralNetwork()

      // Create 3D text using formal font with multi-color effect
      const loader = new FontLoader()
      textMesh = new THREE.Group()
      
      // Use a more formal font - Gentilis Bold
      loader.load('https://threejs.org/examples/fonts/gentilis_bold.typeface.json', (font) => {
        const text = 'VNour'
        const letterSpacing = 0.1
        let totalWidth = 0
        
        // Calculate total width for centering
        text.split('').forEach((char) => {
          const charGeometry = new TextGeometry(char, {
            font: font,
            size: 1.2,
            height: 0.35,
            curveSegments: 16,
            bevelEnabled: true,
            bevelThickness: 0.08,
            bevelSize: 0.08,
            bevelOffset: 0,
            bevelSegments: 8
          })
          charGeometry.computeBoundingBox()
          totalWidth += charGeometry.boundingBox.max.x - charGeometry.boundingBox.min.x + letterSpacing
        })
        
        // Create each letter with different colors
        let currentX = -totalWidth / 2
        const colors = [
          0xffffff, // V - White
          0x0066ff, // N - Blue
          0x00ff88, // o - Green
          0x0066ff, // u - Blue
          0xffffff  // r - White
        ]
        
        text.split('').forEach((char, index) => {
          const charGeometry = new TextGeometry(char, {
            font: font,
            size: 1.2,
            height: 0.35,
            curveSegments: 16,
            bevelEnabled: true,
            bevelThickness: 0.08,
            bevelSize: 0.08,
            bevelOffset: 0,
            bevelSegments: 8
          })
          
          charGeometry.computeBoundingBox()
          const charWidth = charGeometry.boundingBox.max.x - charGeometry.boundingBox.min.x
          
          const isWhite = colors[index] === 0xffffff
          const charMaterial = new THREE.MeshStandardMaterial({
            color: colors[index],
            emissive: isWhite ? 0xffffff : colors[index],
            emissiveIntensity: isWhite ? 0.5 : 0.7,
            metalness: 0.0,
            roughness: 0.1
          })
          
          const charMesh = new THREE.Mesh(charGeometry, charMaterial)
          charMesh.position.x = currentX + charWidth / 2
          currentX += charWidth + letterSpacing
          
          textMesh.add(charMesh)
        })
        
        textMesh.position.y = 0
        scene.add(textMesh)
      })

      // Animation loop
      const animate = () => {
        animationId = requestAnimationFrame(animate)
        time += 0.005

        // Very slow, subtle rotation
        if (textMesh && textMesh.children.length > 0) {
          textMesh.rotation.y += 0.002
          textMesh.rotation.x += 0.001
        }

        // Animate neural network nodes - subtle movement
        neuralNodes.forEach((node, index) => {
          const initialPos = nodeInitialPositions[index]
          node.position.x = initialPos.x + Math.cos(time * 0.3 + index) * 0.2
          node.position.y = initialPos.y + Math.sin(time * 0.4 + index) * 0.2
          node.position.z = initialPos.z + Math.sin(time * 0.2 + index) * 0.15
          // Subtle pulse effect
          const scale = 1 + Math.sin(time * 1.0 + index) * 0.1
          node.scale.set(scale, scale, scale)
        })

        // Update connections to follow nodes
        neuralConnections.forEach((connection) => {
          const node1Index = connection.userData.node1
          const node2Index = connection.userData.node2
          const positions = connection.geometry.attributes.position
          if (positions) {
            positions.setXYZ(0, 
              neuralNodes[node1Index].position.x,
              neuralNodes[node1Index].position.y,
              neuralNodes[node1Index].position.z
            )
            positions.setXYZ(1,
              neuralNodes[node2Index].position.x,
              neuralNodes[node2Index].position.y,
              neuralNodes[node2Index].position.z
            )
            positions.needsUpdate = true
          }
          // Pulse effect
          const opacity = 0.1 + Math.sin(time * 1.5 + node1Index) * 0.1
          connection.material.opacity = Math.max(0.05, opacity)
        })

        // Render with bloom post-processing
        composer.render()
      }
      animate()

      // Handle window resize
      const handleResize = () => {
        camera.aspect = window.innerWidth / window.innerHeight
        camera.updateProjectionMatrix()
        renderer.setSize(window.innerWidth, window.innerHeight)
        composer.setSize(window.innerWidth, window.innerHeight)
      }
      window.addEventListener('resize', handleResize)
    }

    onMounted(() => {
      initThreeJS()
    })

    onUnmounted(() => {
      if (animationId) {
        cancelAnimationFrame(animationId)
      }
      if (renderer) {
        renderer.dispose()
      }
      if (composer) {
        composer.dispose()
      }
      window.removeEventListener('resize', handleResize)
    })

    return {
      container
    }
  }
}
</script>

<style scoped>
.three-container {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
</style>

