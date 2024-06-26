<template>
  <div id="viewport">
    <!-- To this element we will append our 3d scene. -->
    <div id="threejs-container"></div>

</div>
</template>

<script setup>
// Imports;
import { onMounted, onUpdated, computed } from 'vue'
import * as THREE from "three"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { Rhino3dmLoader } from "three/addons/loaders/3DMLoader.js"
import { runCompute } from "@/scripts/compute.js"
import { loadRhino } from "@/scripts/compute.js";

const loader = new Rhino3dmLoader()
loader.setLibraryPath('https://cdn.jsdelivr.net/npm/rhino3dm@8.0.0-beta2/')


const props = defineProps(["data", "path"]);
const emits = defineEmits(["updateMetadata"]);


// Three js objects
let renderer, camera, scene,  controls, container


function init() {
  // https://threejs.org/docs/#api/en/renderers/WebGLRenderer
  // This object will render our scene
  renderer = new THREE.WebGLRenderer()

  container = document.getElementById("threejs-container")

  // Rendered needs to know what's the size of the scene. 
  renderer.setSize(container.offsetWidth, container.offsetHeight)

  // We are taking element defined in <template> and appending our render to it. 
    container.appendChild(renderer.domElement)

  // https://threejs.org/docs/#api/en/cameras/PerspectiveCamera
  camera = new THREE.PerspectiveCamera(20, container.offsetWidth / container.offsetHeight, 0.1, 100)
  camera.position.set(2, 3, 7)

  


  // https://threejs.org/docs/?q=scene#api/en/scenes/Scene
  scene = new THREE.Scene()
  scene.background = new THREE.Color("#f5f6fa")

  // orbit controls
  controls = new OrbitControls(camera, renderer.domElement)



  // add some ambient light here
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5) 
  scene.add(ambientLight)

  // Directional Light
  const directionalLight = new THREE.DirectionalLight(0x008080, 1) 
  directionalLight.position.set(1, 1, 1) 
  directionalLight.castShadow = true /
  scene.add(directionalLight)

  // Set up shadow properties for the light
  directionalLight.shadow.mapSize.width = 1024
  directionalLight.shadow.mapSize.height = 1024
  directionalLight.shadow.camera.near = 0.5
  directionalLight.shadow.camera.far = 500
  directionalLight.shadow.camera.left = -100
  directionalLight.shadow.camera.right = 100
  directionalLight.shadow.camera.top = 100
  directionalLight.shadow.camera.bottom = -100



  // add fun shape
  animate()
}


// this function runs Compute
async function compute() {
  console.log("Runnning compute... \ndata sent: ", props.data)
  const doc = await runCompute(props.data, props.path)

  if (doc.metadata) {
    emits("updateMetadata", doc.metadata);
  }

  // clear objects from scene
  scene.traverse((child) => {
    if (!child.isLight) {
      scene.remove(child)
    }
  })


  const buffer = new Uint8Array(doc.toByteArray()).buffer;
  loader.parse(buffer, function (object) {
    ///////////////////////////////////////////////////////////////////////
    // add object graph from rhino model to three.js scene

    let material = new THREE.MeshNormalMaterial()

    object.traverse((child) => {

      console.log(child)
      if (child.isMesh) {
        child.material = material
      }
    })

    


    scene.add(object);
    console.log("Compute done")
  });
}



// for controls update
function animate() {
  // https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame
  // native Javascript function that tells your browser you are animating!
  requestAnimationFrame(animate);
  controls.update();


  renderer.render(scene, camera);
}

// This will be run whenever the window is resized
window.addEventListener("resize", onWindowResize);
function onWindowResize() {

  const height = container.offsetHeight
  const width = container.offsetWidth

  camera.aspect = width/ height
  camera.updateProjectionMatrix();


  renderer.setSize(width, height)

}


// This will be run whenever this component is instantiated
onMounted(async() => {
  init()
  await loadRhino()
  compute();
})

//onUpdated is called when an input prop is changed
onUpdated(() => {
  compute();
})



</script>

<style scoped>


#viewport {

  height: 100%;
  width: 100%;
  min-width: 200px;
  position:inherit;
}

#threejs-container {
  height: 100%;
  width: 100%;
  min-width: 200px;
  position:inherit;
}

</style>