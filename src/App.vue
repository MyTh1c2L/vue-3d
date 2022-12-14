<template>
  <div>
    <button @click="getTest1" color="success" class="mr-4 mt-2">
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
      faces: [],
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
    getTest1() {
      axios.get("http://localhost:8080/test1").then((data) => {
        this.points = data.data.pointsIn;
        this.vertices = data.data.pointsOut;
        this.faces = data.data.faces;
        this.init();
      });
    },
    getBody() {
      axios.get("http://localhost:8080/getBody").then((data) => {
        this.points = data.data.pointsIn;
        this.vertices = data.data.pointsOut;
        this.faces = data.data.faces;
        this.init();
      });
    },
    init() {
      let camera, scene, renderer;
      scene = new THREE.Scene();
      scene.background = new THREE.Color(0xaaaaaa);

      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // camera

      camera = new THREE.PerspectiveCamera(
        90,
        window.innerWidth / window.innerHeight,
        1,
        2000
      );
      camera.position.set(55, 60, 50);
      scene.add(camera);

      // controls
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI / 2;

      // ambient light
      scene.add(new THREE.AmbientLight(0x222222));

      // point light
      const light = new THREE.PointLight(0xffffff, 0.5);
      camera.add(light);

      // helper
      scene.add(new THREE.AxesHelper(100));

      // vertices
      let vertices3d = [];
      //let normals = [];
      for (let i = 0; i < this.vertices.length; i++) {
        let x = this.vertices[i].x;
        let y = this.vertices[i].y;
        let z = this.vertices[i].z;
        let vector = new THREE.Vector3(x, y, z);
        vertices3d[i] = vector;
        //vertices3d.push(x, y, z);
        //normals.push( 0, 0, 1 );
      }

      // faces
      let faces = [];
      for (let i = 0; i < this.faces.length; i++) {
        let x = this.faces[i][0];
        let y = this.faces[i][1];
        let z = this.faces[i][2];
        faces.push(x, y, z);
      }

      /*const geometry = new THREE.BufferGeometry();
			geometry.setIndex( faces );
			geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices3d, 3 ) );
      geometry.setAttribute( 'normal', new THREE.Float32BufferAttribute( normals, 3 ) );
      const material = new THREE.MeshNormalMaterial();
      material.opacity = 0.8;
      material.transparent = true;
      material.flatShading = true;
      material.side = THREE.DoubleSide
      const mesh = new THREE.Mesh(geometry, material);
      scene.add(mesh);*/

      const geometry = new ConvexGeometry(vertices3d);
      const material = new THREE.MeshNormalMaterial();
      material.opacity = 0.4;
      material.transparent = true;
      material.flatShading = true;
      const mesh = new THREE.Mesh(geometry, material);
      scene.add(mesh);

      scene.add(
        new THREE.Points(
          new THREE.BufferGeometry().setFromPoints(vertices3d),
          new THREE.PointsMaterial({
            color: 0xFA8072,
            size: 0.1,
          })
        )
      );

      // points
      let points3d = [];
      for (let i = 0; i < this.points.length; i++) {
        let x = this.points[i].x;
        let y = this.points[i].y;
        let z = this.points[i].z;
        let vector = new THREE.Vector3(x, y, z);
        points3d[i] = vector;
      }

      scene.add(
        new THREE.Points(
          new THREE.BufferGeometry().setFromPoints(points3d),
          new THREE.PointsMaterial({
            color: 0x000000,
            size: 0.1,
          })
        )
      );

      // eslint-disable-next-line
      function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
      }
      animate();
    },
  }
};
</script>