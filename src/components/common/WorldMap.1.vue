<template>
  <div ref="worldWrapper">
  </div>
</template>
<script>
import * as THREE from 'three'
import * as effectcomposer from 'three-effectcomposer'
import EarthImg from '@/assets/images/world/Earth.png'
import EarthSpecImg from '@/assets/images/world/EarthSpec.png'
import EarthNormalImg from '@/assets/images/world/EarthNormal.png'

// import RenderPass from '@/libs/postprocessing/RenderPass'
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
      spotLight: null,
      composer: null
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
      this.webGLRenderer.shadowMap.enabled = true
      // 球體
      this.sphere = this.createMesh(new THREE.SphereGeometry(10, 40, 40))
      this.scene.add(this.sphere)

      this.camera.position.x = -10
      this.camera.position.y = 15
      this.camera.position.z = 25

      this.camera.lookAt(new THREE.Vector3(0, 0, 0))

      this.scene.add(this.camera)

      // this.orbitControls = new THREE.OrbitControls(this.camera)
      // this.orbitControls.autoRotate = false

      this.clock = new THREE.Clock()

      this.ambi = new THREE.AmbientLight(0x181818)
      this.scene.add(this.ambi)

      this.spotLight = new THREE.DirectionalLight(0xffffff)
      this.spotLight.position.set(550, 100, 550)
      this.spotLight.intensity = 0.6
      this.scene.add(this.spotLight)

      let renderPass = new THREE.RenderPass(this.scene, this.camera)
      let effectCopy = new THREE.ShaderPass(THREE.CopyShader)
      effectCopy.renderToScreen = true
      let shaderPass = new THREE.ShaderPass(THREE.CustomGrayScaleShader)
      shaderPass.enabled = false
      let bitPass = new THREE.ShaderPass(THREE.CustomBitShader)
      bitPass.enabled = false

      this.composer = new THREE.EffectComposer(this.webGLRenderer)
      this.composer.addPass(renderPass)
      this.composer.addPass(shaderPass)
      this.composer.addPass(bitPass)
      this.composer.addPass(effectCopy)

      this.$refs.worldWrapper.append(this.webGLRenderer.domElement)
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

      // 定義一個基礎材質測試 MeshBasicMaterial
      // let meshBasicMaterial = new THREE.MeshBasicMaterial({color: 0xcccccc})

      let mesh = THREE.SceneUtils.createMultiMaterialObject(geom, [planetMaterial])
      // let mesh = new THREE.Mesh(geom, meshBasicMaterial)
      return mesh
    },
    render () {
      console.log('執行渲染函數')
      // console.log(this.scene, this.camera)
      let delta = this.clock.getDelta()
      console.log(delta)
      this.orbitControls.update(delta)
      this.webGLRenderer.render(this.scene, this.camera)
    }
  },
  mounted () {
    this.initScene()
    this.render()
    // this.createMesh()
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


