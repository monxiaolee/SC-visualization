<template>
  <div ref='model'>
    <div ref='vertexShader'>
      varying vec3 vNormal;
			varying vec2 vUv;
			void main() {
				gl_Position = projectionMatrix * modelViewMatrix * vec4( position, 1.0);
				vNormal = normalize( normalMatrix * normal );
				vUv = uv;
			}
    </div>
    <div ref="globeFragmentShader">
      uniform sampler2D mapIndex;
			uniform sampler2D lookup;
			uniform sampler2D outline;
			uniform float outlineLevel;
			varying vec3 vNormal;
			varying vec2 vUv;
			void main() {

				vec4 mapColor = texture2D( mapIndex, vUv );
				float indexedColor = mapColor.x;
				vec2 lookupUV = vec2( indexedColor, 0. );
				vec4 lookupColor = texture2D( lookup, lookupUV );
				float mask = lookupColor.x + (1.-outlineLevel) * indexedColor;
				mask = clamp(mask,0.,1.);
				float outlineColor = texture2D( outline, vUv ).x * outlineLevel;
				float diffuse = mask + outlineColor;
				gl_FragColor = vec4( vec3(diffuse), 1.  );
				// gl_FragColor = vec4( lookupColor );
				// gl_FragColor = vec4(texture2D( lookup, vUv ).xyz,1.);
			}
    </div>
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
      imgUrl: require('../../assets/images/material/map_indexed.png'),
      scene: null,
      camera: null,
      normalMaterial: null,
      mapIndexedImage: null,
      mapOutlineImage: null,
      shaderMaterial: null,
      globeFragmentShader: '',
      uniforms: null
    }
  },
  methods: {
    start () {
      this.mapIndexedImage = new Image()
      // this.mapIndexedImage.src = '../../assets/'
      this.mapIndexedImage.src = this.imgUrl
      console.log(this.imgUrl)
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

      // 定义一个基础材质测试备用
      // this.normalMaterial = new THREE.MeshNormalMaterial({})

      let lookupTexture = new THREE.Texture(lookupCanvas)
      lookupTexture.magFilter = THREE.NearestFilter
      lookupTexture.minFilter = THREE.NearestFilter
      lookupTexture.needsUpdate = true

      let indexedMapTexture = new THREE.Texture(this.mapIndexedImage)
      indexedMapTexture.needsUpdate = true
      indexedMapTexture.magFilter = THREE.NearestFilter
      indexedMapTexture.minFilter = THREE.NearestFilter

      let outlinedMapTexture = new THREE.Texture(this.mapOutlineImage)
      outlinedMapTexture.needsUpdate = true

      this.uniforms = {
        // 'mapIndex': {type: 't', value: 0, texture: indexedMapTexture}
        'mapIndex': {type: 't', value: 0, texture: new THREE.TextureLoader().load(this.imgUrl)}
        // 'lookup': {type: 't', value: 1, texture: lookupTexture},
        // 'outline': {type: 't', value: 2, texture: outlinedMapTexture},
        // 'outlineLevel': {type: 'f', value: 1}
      }

      // 顶点材质
      // this.vertexShader = 'varying vec3 vNormal,' +
      // 'varying vec2 vUv,' +
      // 'void main () {' +
      // 'gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0),' +
      // 'vNormal = normalize(normalMatrix * normal),' +
      // 'vUv = uv,' +
      // '}'

      // this.globeFragmentShader = 'uniform sampler2D mapIndex,' +
      // 'uniform sampler2D mapIndex,' +
      // 'uniform sampler2D lookup,' +
      // 'uniform sampler2D outline,' +
      // 'uniform float outlineLevel,' +
      // 'varying vec3 vNormal,' +
      // 'varying vec2 vUv,' +
      // 'void main () {' +
      // 'vec4 mapColor = texture2D(mapIndex, vUv),' +
      // 'float indexedColor = mapColor.x,' +
      // 'vec2 lookupUV = vec2(indexedColor, 0.),' +
      // 'vec4 lookupColor = texture2D(lookup, lookupUV),' +
      // 'float mask = lookupColor.x + (1.-outlineLevel) * indexedColor,' +
      // 'mask = clamp(mask,0.,1.),' +
      // 'float outlineColor = texture2D(outline, vUv).x * outlineLevel,' +
      // 'float diffuse = mask + outlineColor,' +
      // 'gl_FragColor = vec4(vec3(diffuse), 1.),' +
      // '}'

      this.shaderMaterial = new THREE.ShaderMaterial({
        uniforms: this.uniforms,
        // vertexShader: this.vertexShader
        // fragmentShader: this.globeFragmentShader
        vertexShader: this.$refs.vertexShader.textContent,
        fragmentShader: this.$refs.globeFragmentShader.textContent
      })

      console.log(this.shaderMaterial)

      // let backMat = new THREE.MeshBasicMaterial({})
      let sphere = new THREE.Mesh(new THREE.SphereGeometry(100, 40, 40), this.shaderMaterial)
      sphere.doubleSided = false
      sphere.rotation.x = Math.PI
      sphere.rotation.y = -Math.PI / 2
      sphere.rotation.z = Math.PI

      rotating.add(sphere)
      // this.scene.add(sphere)

      // 定义摄像机
      this.camera = new THREE.PerspectiveCamera(12, window.innerWidth / window.innerHeight, 1, 20000)
      this.camera.position.z = 1400
      this.camera.position.y = 0
      this.camera.lookAt(this.scene.width / 2, this.scene.height / 2)
      this.scene.add(this.camera)

      let renderer = new THREE.WebGLRenderer({antialias: false})
      renderer.setSize(window.innerWidth, window.innerHeight)
      // renderer.autoClear = false
      // renderer.sortObjects = false
      // renderer.generateMipmaps = false

      renderer.render(this.scene, this.camera)

      this.$refs.model.appendChild(renderer.domElement)
      console.log(renderer.domElement)
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