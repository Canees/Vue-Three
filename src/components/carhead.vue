<template>
  <div class="car" id="carbox">
    <!-- 刻度 -->
    <div class="chart1"></div>
  </div>
</template>
<script>
import * as Three from 'three'
import { OBJLoader, MTLLoader } from 'three-obj-mtl-loader'
const OrbitControls = require('three-orbit-controls')(Three) // 控制器
// const FBXLoader = require('three-fbx-loader') // Fbx加载器
export default {
  // props: ['systemMove'],
  data () {
    return {
      renderer: null, // 渲染器
      scene: null, // 场景
      OrbitControls: null, // 控制器
      camera: null, // 相机
      rings: {
        ring1: null,
        ring2: null,
        ring3: null
      },
      carhead: null // 车头数据
    }
  },
  methods: {
    // 初始化
    initThree () {
      const dom = document.getElementById('carbox')
      // 场景
      this.scene = new Three.Scene()
      // 渲染器+透明度
      this.renderer = new Three.WebGLRenderer({
        alpha: true,
        antialias: true
      })
      this.renderer.setPixelRatio(window.devicePixelRatio) //
      this.renderer.setSize(dom.clientWidth, dom.clientHeight) // 场景大小
      dom.appendChild(this.renderer.domElement) // 元素中插入canvas对象
      // 相机
      this.camera = new Three.PerspectiveCamera(20, dom.clientWidth / dom.clientHeight, 1, 1000) // 透视相机
      this.camera.position.set(20, 10, 200)
      // 控制器
      this.OrbitControls = new OrbitControls(this.camera, this.renderer.domElement) // 控制器
      // 环境光
      this.loadlight()
      // 加载模型OBJ+MTL
      this.loadOBJMTL()
      // 直接加载贴图+1外环
      this.rings.ring1 = this.gourpThreemodel()
      this.rings.ring2 = this.gourpThreemodel()
      this.rings.ring3 = this.gourpThreemodel()
      this.rings.ring2.position.set(10, 3, -17)
      this.rings.ring3.position.set(25, 7, -45)
      this.scene.add(this.rings.ring1, this.rings.ring2, this.rings.ring3)
      // 坐标
      this.loadXYZHelper()
      // 渲染
      this.renders()
    },
    // 动画渲染
    renders () {
      this.renderer.render(this.scene, this.camera) // 渲染场景
      // this.OrbitControls.update() // 更新控制器
      // 环1
      this.rings.ring1.children[0].rotateZ(-0.01)
      this.rings.ring1.children[1].rotateZ(0.01)
      this.rings.ring1.children[2].rotateZ(-0.01)
      // 环2
      this.rings.ring2.children[0].rotateZ(-0.01)
      this.rings.ring2.children[1].rotateZ(0.01)
      this.rings.ring2.children[2].rotateZ(-0.01)
      // 环3
      this.rings.ring3.children[0].rotateZ(-0.01)
      this.rings.ring3.children[1].rotateZ(0.01)
      this.rings.ring3.children[2].rotateZ(-0.01)
      requestAnimationFrame(this.renders)
    },
    // 辅助坐标
    loadXYZHelper () {
      const axisHelper = new Three.AxesHelper(200)
      this.scene.add(axisHelper)
    },
    // 光照加载
    loadlight () {
      // 垂直光
      const light = new Three.HemisphereLight(0xeeeeee, 0x00000, 1)
      light.position.set(-8, 30, -60)
      this.scene.add(light)
    },
    // fbx模型加载
    loadfbx () {
      // 加载fbx模型
      // new FBXLoader().load('/model/fbx/a.fbx', object => {
      //   console.log(666, object)
      //   // const mixer = new Three.AnimationMixer(object)
      //   // console.log(777, mixer)
      //   // const action = mixer.clipAction(object.animations[0])
      //   // action.play()
      //   // console.log(888, action)
      //   // object.traverse(function (child) {
      //   //   if (child.isMesh) {
      //   //     child.castShadow = true
      //   //     child.receiveShadow = true
      //   //   }
      //   // })
      //   this.scene.add(object)
      // })
    },
    // obj+mtl车头模型
    loadOBJMTL () {
      new MTLLoader().setPath('/static/model/cas/').load('RH.mtl', materials => {
        materials.preload()
        new OBJLoader()
          .setMaterials(materials)
          .setPath('/static/model/cas/')
          .load('RH.obj', obj => {
            // 模型缩放
            obj.scale.set(0.0044, 0.0044, 0.0044)
            // 设置模型轴心
            obj.children[0].geometry.computeBoundingBox()
            obj.children[0].geometry.center()
            // 设置模型位置
            obj.rotation.set(0.155, -0.5, 0) // 旋转
            obj.position.set(33, 13.5, 108) // 模型位置
            this.carhead = obj
            this.scene.add(obj)
          })
      })
    },
    // 三个环组
    gourpThreemodel () {
      const group1 = new Three.Group() // 三个环一个组
      // 外环1
      const geometry1 = new Three.RingGeometry(11, 19, 132) // 圆环
      const texture1 = Three.ImageUtils.loadTexture('/static/model_img/w@2x.png') // 纹理贴图
      const material1 = new Three.MeshBasicMaterial({
        map: texture1,
        transparent: true,
        side: Three.DoubleSide
      }) // 材质+纹理+双面贴DoubleSide+color: 0xffff00,
      const Mesh1 = new Three.Mesh(geometry1, material1) // 网格模型加载两个
      Mesh1.position.set(13, 8, 147)
      Mesh1.rotation.set(0, -0.5, 0)
      Mesh1.scale.set(0.55, 0.55, 0.55)
      // 中环
      const geometry2 = new Three.RingGeometry(7.8, 19, 132) // 几何图形
      const texture2 = Three.ImageUtils.loadTexture('/static/model_img/z@2x.png')
      const material2 = new Three.MeshBasicMaterial({
        map: texture2,
        transparent: true,
        side: Three.DoubleSide
      })
      const Mesh2 = new Three.Mesh(geometry2, material2) // 网格模型加载两个
      Mesh2.position.set(13, 8, 147)
      Mesh2.rotation.set(0, -0.5, 0)
      Mesh2.scale.set(0.5, 0.5, 0.5)
      // 内环
      const geometry3 = new Three.RingGeometry(5, 10, 132) // 几何图形
      const texture3 = Three.ImageUtils.loadTexture('/static/model_img/n@2x.png')
      const material3 = new Three.MeshBasicMaterial({
        map: texture3,
        transparent: true,
        side: Three.DoubleSide
      })
      const Mesh3 = new Three.Mesh(geometry3, material3) // 网格模型加载两个
      Mesh3.position.set(13, 8, 147)
      Mesh3.scale.set(0.75, 0.75, 0.75)
      Mesh3.rotation.set(0, -0.5, 0)
      // 添加组+场景增加
      group1.add(Mesh1, Mesh2, Mesh3)
      return group1
    }
  },
  mounted () {
    this.initThree()
  },
  // watch: {
  //   systemMove: {
  //     handler (n) {
  //       // Math.degToRad 度数转弧度
  //       const x = Three.Math.degToRad(n.horizontal)
  //       const y = 0.155 - Three.Math.degToRad(n.vertical)
  //       if (this.carhead) {
  //         this.carhead.rotation.z = x
  //         this.carhead.rotation.x = y
  //       }
  //     },
  //     deep: true
  //   }
  // }
}
</script>
<style scoped>
.car {
  /* border: solid 1px red; */
  width: 1290px;
  height: 700px;
  position: relative;
  background: #333;
}
</style>
