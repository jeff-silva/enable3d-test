<template>
  <div style="position:relative; border:solid 1px blue;">
    <canvas ref="canvas" style="width:100%;"></canvas>
    <template v-if="three">
      <slot />
    </template>
  </div>
</template>

<script>
  import * as THREE from 'three';
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
  import { AmmoPhysics, ExtendedMesh, PhysicsLoader } from '@enable3d/ammo-physics';

  export default {
    data() {
      return {
        three: false,
      };
    },
    methods: {
      threejsInit() {
        PhysicsLoader('/ammo', () => {
          const threejsAnimation = () => {
            this.parentCall('onUpdate');
            requestAnimationFrame(threejsAnimation);
            renderer.render(scene, camera);
          };

          // Size
          let width = this.$el.offsetWidth;
          let height = this.$el.offsetHeight;

          // Renderer
          let renderer = new THREE.WebGLRenderer( { antialias: true } );
          renderer.setSize(width, height);
          renderer.setAnimationLoop(threejsAnimation);
          this.$refs.canvas.replaceWith(renderer.domElement);
          let canvas = this.$refs.canvas;
          
          // Camera
          let camera = new THREE.PerspectiveCamera( 70, width / height, 0.01, 10 );
          camera.position.x = 5;
          camera.position.y = 5;
          camera.position.z = 5;
          
          // Scene
          let scene = new THREE.Scene();
          scene.background = new THREE.Color(0xf0f0f0);

          // Light
          let light = new THREE.AmbientLight(0xffffff);
          scene.add(light);

          // Orbit control
          new OrbitControls(camera, this.$el);

          // Grid Helper
          const gridHelper = new THREE.GridHelper( 400, 40, 0x0000ff, 0x808080 );
          scene.add( gridHelper );

          // Default object
          // const geometry = new THREE.BoxGeometry( 0.2, 0.2, 0.2 );
          // const material = new THREE.MeshNormalMaterial();
          // const mesh = new THREE.Mesh( geometry, material );
          // scene.add( mesh );

          this.three = { width, height, renderer, camera, scene, light, THREE };

          // Resize
          window.addEventListener('resize', () => {
            width = this.$el.offsetWidth;
            height = this.$el.offsetHeight;
            camera.aspect = width / height;
            camera.updateProjectionMatrix();
            renderer.setSize(width, height);
          });

          // Start method
          this.parentCall('onCreate');
        });
      },
      parentCall(methodName, params = {}) {
        if (! typeof this.$parent[ methodName ] == 'function') return;
        this.$parent[ methodName ]({ ...this.three, ...params });
      },
    },
    mounted() {
      this.threejsInit();
    },
  };
</script>