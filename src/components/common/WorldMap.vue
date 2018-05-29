<template>
  <div ref="worldWrapper">
  </div>
</template>
<script>
import * as THREE from 'three'
import EarthImg from '@/assets/images/world/Earth.png'
import EarthSpecImg from '@/assets/images/world/EarthSpec.png'
import EarthNormalImg from '@/assets/images/world/EarthNormal.png'

export default {
  name: 'WorldMap',
  data () {
    return {
      earthImg: EarthImg,
      earthSpecImg: EarthSpecImg,
      earthNormalImg: EarthNormalImg,
      scene: null,
      camera: null,
      orbitControls: null,
      webGLRenderer: null,
      sphere: null,
      clock: null,
      ambi: null,
      spotLight: null
    }
  },
  methods: {
    initScene () {
      // 場景
      this.scene = new THREE.Scene()
      // 攝像機
      this.camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
      // 渲染器
      this.webGLRenderer = new THREE.WebGLRenderer()
      this.webGLRenderer.setClearColor(0x000, 1.0)
      this.webGLRenderer.setSize(window.innerWidth, window.innerHeight)
      this.webGLRenderer.shadowMapEnabled = true
      // 球體
      this.sphere = this.createMesh(new THREE.SphereGeometry(10, 40, 40))
      this.scene.add(this.sphere)

      this.camera.position.x = -10
      this.camera.position.y = 15
      this.camera.position.z = 25

      this.camera.lookAt(new THREE.Vector3(0, 0, 0))

      this.orbitControls = new THREE.OrbitControls(this.camera)
      this.orbitControls.autoRotate = false

      this.clock = new THREE.Clock()

      this.ambi = new THREE.AmbientLight(0x181818)
      this.scene.add(this.ambi)

      this.spotLight = new THREE.DirectionalLight(0xffffff)
      this.spotLight.position.set(550, 100, 550)
      this.spotLight.intensity = 0.6
      this.scene.add(this.spotLight)

      this.$refs.worldWrapper.append(this.webGLRenderer.domElement)

      console.log('--------------')
      console.log(this.sphere)
    },
    createMesh (geom) {
      let planetTexture = THREE.ImageUtils.loadTexture(this.EarthImg)
      let specularTexture = THREE.ImageUtils.loadTexture(this.earthSpecImg)
      let normalTexture = THREE.ImageUtils.loadTexture(this.EarthNormalImg)

      let planetMaterial = new THREE.MeshPhongMaterial()
      planetMaterial.specularMap = specularTexture
      planetMaterial.specular = new THREE.Color(0x4444aa)

      planetMaterial.normalMap = normalTexture
      planetMaterial.map = planetTexture

      let mesh = THREE.SceneUtils.createMultiMaterialObject(geom, [planetMaterial])
      console.log(planetTexture, specularTexture, normalTexture, planetMaterial)
      return mesh
    }
  },
  mounted () {
    this.initScene()
    this.createMesh()
  },
  components: {}
}
</script>
<style lang="less" scoped>
h1 {
  color: #ffffff;
  font-size: 14px;
}
</style>


