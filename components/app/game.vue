<template>
  <app-threejs>
    <div>Hello</div>
    <input type="number" v-model.number="mesh.position.x">
    <input type="number" v-model.number="mesh.position.y">
    <input type="number" v-model.number="mesh.position.z">
  </app-threejs>
</template>

<script>
  import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

  export default {
    data() {
      return {
        mesh: false,
      };
    },
    methods: {
      onCreate({ THREE, scene }) {

        const geometry = new THREE.BoxGeometry( 0.2, 0.2, 0.2 );
        const material = new THREE.MeshNormalMaterial();
        scene.add(this.mesh = new THREE.Mesh( geometry, material ));

        new GLTFLoader().setPath('assets/models/terrain/').load('scene.gltf', (gltf) => {
          scene.add(gltf.scene);
        });

        // console.log('game:onCreate');
      },
      onUpdate({ camera }) {
        // this.mesh.position.x += 0.00001;
        camera.lookAt(this.mesh.position);

        // console.log('game:onUpdate');
        // this.camera.lookAt(this.sphere.absolutePosition);
      },
    },
  };
</script>