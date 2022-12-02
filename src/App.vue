<template>
  <div>
    <button @click="getVertices" color="success" class="mr-4 mt-2">
      Вывести модель
    </button>
  </div>
  <canvas id="canvas3D"></canvas>
</template>
  
<script>
import axios from "axios";
import * as THREE from "three";
//import * as BufferGeometryUtils from "three/examples/jsm/utils/BufferGeometryUtils";
import { ConvexGeometry } from "three/examples/jsm/geometries/ConvexGeometry";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
export default {
  name: "App",
  data() {
    return {
      model: "",
      points: [],
      vertices: [],
    };
  },
  methods: {
    getVertices() {
      axios.get("http://localhost:8080/getPoints").then((data) => {
        this.points = data.data.pointsIn;
        this.vertices = data.data.pointsOut;
        this.init();
      });
    },
    init() {
      console.log(this.vertices);
      let camera, scene, renderer;
      scene = new THREE.Scene();

      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // camera

      camera = new THREE.PerspectiveCamera(
        40,
        window.innerWidth / window.innerHeight,
        1,
        1000
      );
      camera.position.set(15, 20, 30);
      scene.add(camera);

      // controls
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI / 2;
      // ambient light

      scene.add(new THREE.AmbientLight(0x222222));

      // point light

      const light = new THREE.PointLight(0xffffff, 1);
      camera.add(light);

      // helper
      scene.add(new THREE.AxesHelper(20));
     
      // points
      let points3d = [];
      for (let i = 0; i < this.vertices.length; i++) {
        let x = this.vertices[i].x;
        let y = this.vertices[i].y;
        let z = this.vertices[i].z;
        let vector = new THREE.Vector3(x, y, z);
        points3d[i] = vector;
      }
      console.log(points3d)

      //let dodecahedronGeometry = new THREE.DodecahedronGeometry(10);
      //let dodecahedronGeometry = new CG(this.vertices);
      const geometry = new ConvexGeometry( points3d );
      const material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } ); // TODO Поменять материал
      const mesh = new THREE.Mesh( geometry, material );
      scene.add( mesh );

      // eslint-disable-next-line
      function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
      }
      animate();
    },
  },
};
</script>