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
      scene: null
    }
  },
  methods: {
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

      // console.log(this.scene)
    }
  },
  created () {
  },
  mounted () {
    this.initScene()
  },
  components: {}
}
</script>