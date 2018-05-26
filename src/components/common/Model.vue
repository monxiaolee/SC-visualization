<template>
  <div ref='model'>
  </div>
</template>
<script>
import * as THREE from 'three'
// import * as STATS from 'stats-js'
// import * as DAT from 'dat.gui'

// let scene = new THREE.Scene()
// let camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
// let renderer = new THREE.WebGLRenderer()

export default {
  data () {
    return {
      size: 10,
      height: 20,
      color: '#ff0000',
      scene: null,
      camera: null,
      mapIndexedImage: null,
      shaderMaterial: null
    }
  },
  methods: {
    start () {
      this.mapIndexedImage = new Image()
      // this.mapIndexedImage.src = '../../assets/'
      this.mapIndexedImage.src = '../../assets/images/material/map_indexed.png'
    },
    initScene () {
      this.scene = new THREE.Scene()
      this.scene.matrixAutoUpdate = false

      this.scene.add(new THREE.AmbientLight(0x505050))

      let light1 = new THREE.SpotLight(0xeeeeee, 3)
      light1.position.x = 730
      light1.position.y = 520
      light1.position.z = 626
      light1.castShadow = true

      // 添加第一个灯光
      this.scene.add(light1)

      let rotating = new THREE.Object3D()
      this.scene.add(rotating)

      let lookupCanvas = document.createElement('canvas')
      lookupCanvas.width = 256
      lookupCanvas.height = 1

      this.shaderMaterial = new THREE.ShaderMaterial({
        color: 0xffffff
      })

      let lookupTexture = new THREE.Texture(lookupCanvas)
      lookupTexture.magFilter = THREE.NearestFilter
      lookupTexture.minFilter = THREE.NearestFilter
      lookupTexture.needsUpdate = true

      let indexedMapTexture = new THREE.Texture(this.mapIndexedImage)
      indexedMapTexture.needsUpdate = true
      indexedMapTexture.magFilter = THREE.NearestFilter
      indexedMapTexture.minFilter = THREE.NearestFilter
      // let backMat = new THREE.MeshBasicMaterial({})
      let sphere = new THREE.Mesh(new THREE.SphereGeometry(100, 40, 40), this.shaderMaterialshaderMaterial)
      sphere.doubleSided = false
      sphere.rotation.x = Math.PI
      sphere.rotation.y = -Math.PI / 2
      sphere.rotation.z = Math.PI

      // 定义摄像机
      this.camera = new THREE.PerspectiveCamera(12, window.innerWidth / window.innerHeight, 1, 20000)
      this.camera.position.z = 1400
      this.camera.position.y = 0
      this.camera.lookAt(this.scene.width / 2, this.scene.height / 2)
      this.scene.add(this.camera)

      let renderer = new THREE.WebGLRenderer({antialias: false})
      renderer.setSize(window.innerWidth, window.innerHeight)
      renderer.autoClear = false

      renderer.sortObjects = false
      renderer.generateMipmaps = false

      this.$refs.model.appendChild(renderer.domElement)

      console.log(sphere)
    }
  },
  created () {
  },
  mounted () {
    this.start()
    this.initScene()
  },
  components: {}
}
</script>