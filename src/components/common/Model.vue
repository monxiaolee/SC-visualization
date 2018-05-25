<template>
  <div ref='model'>
  </div>
</template>
<script>
import * as THREE from 'three'
import * as STATS from 'stats-js'
import * as DAT from 'dat.gui'

let scene = new THREE.Scene()
let camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
let renderer = new THREE.WebGLRenderer()

export default {
  data () {
    return {
      size: 10,
      height: 20,
      color: '#ff0000'
    }
  },
  methods: {
    initScene () {
      scene.overrideMaterial = new THREE.MeshLambertMaterial({color: 0xff0000})

      // renderer.setClearColorHex(0xEEEEEE, 1.0)
      renderer.setSize(window.innerWidth, window.innerHeight)
      renderer.shadowMapEnabled = true

      let planeGeometry = new THREE.PlaneGeometry(60, 40, 1, 1)
      let planeMaterial = new THREE.MeshLambertMaterial({color: 0xffffff})
      let plane = new THREE.Mesh(planeGeometry, planeMaterial)
      plane.receiveShadow = true

      plane.rotation.x = -0.5 * Math.PI
      plane.position.x = 0
      plane.position.y = 0
      plane.position.z = 0

      scene.add(plane)

      let ambientLight = new THREE.AmbientLight(0x0c0c0c)
      scene.add(ambientLight)

      let spotLight = new THREE.SpotLight(0xffffff)
      spotLight.position.set(-40, 60, -10)
      spotLight.castShadow = true
      scene.add(spotLight)

      let gui = new DAT.GUI()
      console.log(gui, camera, renderer)
    },
    initStats () {
      let stats = new STATS()
      stats.setMode(0)
      stats.domElement.style.position = 'absolute'
      stats.domElement.style.left = '0px'
      stats.domElement.style.top = '0'
      // this.$refs.model.append(stats.domElement)
      return stats
    },
    render () {
      console.log('渲染函數執行')
      requestAnimationFrame(this.render)
      renderer.render(scene, camera)
      this.$refs.model.append(renderer.domElement)
      // console.log(this.renderer)
    }
  },
  created () {
  },
  mounted () {
    this.initStats()
    this.initScene()
    this.render()
  },
  components: {}
}
</script>