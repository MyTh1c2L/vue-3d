<template>
  <div>
    <button @click="init" color="success" class="mr-4 mt-2">
      Вывести модель
    </button>
  </div>
  <canvas id="canvas3D"></canvas>
</template>
  
<script>
/* eslint-disable */
import * as THREE from "three";

import Vertices from "./vertices.js";
import Normals from "./normals.js";
import Indices from "./indices.js";

import * as BufferGeometryUtils from "three/examples/jsm/utils/BufferGeometryUtils";
import { ConvexGeometry } from "three/examples/jsm/geometries/ConvexGeometry";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { BufferGeometry } from 'three';

export default {
  name: "App",
  data() {
    return {
      pointsOrig: [],
      pointsCH: [],
      indicesOrig: [],
      indicesCH: [],
    };
  },
  methods: {
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
        0.1,
        200000
      );
      camera.position.set(12000, 3000, 874);
      scene.add(camera);

      // controls
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI;

      // ambient light
      scene.add(new THREE.AmbientLight(0x222222));

      // point light
      const light = new THREE.PointLight(0xffffff, 0.5);
      camera.add(light);

      // helper
      scene.add(new THREE.AxesHelper(100));

      // vertices
      let points = Vertices.getVertices()

      // let vertices3d = Vertices.getVertices()
      // let normals = Normals.getNormals()
      // let faces = Indices.getIndex()

      // const geometry = new THREE.BufferGeometry();
			// geometry.setIndex( faces );
			// geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices3d, 3 ) );
      // geometry.setAttribute( 'normal', new THREE.Float32BufferAttribute( normals, 3 ) );
      // const material = new THREE.MeshNormalMaterial();
      // material.opacity = 0.8;
      // material.transparent = true;
      // material.flatShading = true;
      // material.side = THREE.DoubleSide
      // const mesh = new THREE.Mesh(geometry, material);
      // scene.add(mesh);

      const geometry = new ConvexGeometry(points);
      const material = new THREE.MeshNormalMaterial();
      material.opacity = 0.4;
      material.transparent = true;
      material.flatShading = true;
      const mesh = new THREE.Mesh(geometry, material);
      scene.add(mesh);

      // for (let i = 0; i < this.indicesCH.length; i+=3) {
      //   let position = new Float32Array([
      //     this.pointsCH[this.indicesCH[i] * 3], this.pointsCH[this.indicesCH[i] * 3 + 1], this.pointsCH[this.indicesCH[i] * 3 + 2],
      //     this.pointsCH[this.indicesCH[i + 1] * 3], this.pointsCH[this.indicesCH[i + 1] * 3 + 1], this.pointsCH[this.indicesCH[i + 1] * 3 + 2],
      //     this.pointsCH[this.indicesCH[i + 2] * 3], this.pointsCH[this.indicesCH[i + 2] * 3 + 1], this.pointsCH[this.indicesCH[i + 2] * 3 + 2]
      //   ]);

      //   console.log(this.indicesCH[i])
      //   console.log(this.indicesCH[i + 1])
      //   console.log(this.indicesCH[i + 2])

      //   const geometry = new BufferGeometry();
      //   geometry.attributes.position = new THREE.BufferAttribute(position, 3);

      //   //let faces = [ 0, 1, 2 ];
      //   //geometry.setIndex(faces);

      //   console.log(geometry.attributes.position)

      //   let mesh = new THREE.Mesh(
      //     geometry,
      //     new THREE.MeshPhongMaterial({ 
      //       color: Math.random() * 0xffffff,
      //       side: THREE.DoubleSide,
      //       opacity: 0.4,
      //       transparent: true,
      //       flatShading: true
      //     })
      //   );
      //   scene.add(mesh);
      // }

      // points
      // let points3d = [];
      // let j2 = 0;
      // for (let i = 0; i < this.pointsCH.length; i += 3) {
      //   points3d[j2] = new THREE.Vector3(this.pointsCH[i], this.pointsCH[i + 1], this.pointsCH[i + 2]);
      //   j2++;
      // }

      // scene.add(
      //   new THREE.Points(
      //     new THREE.BufferGeometry().setFromPoints(points3d),
      //     new THREE.PointsMaterial({
      //       color: 0x000000,
      //       size: 10,
      //     })
      //   )
      // );

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