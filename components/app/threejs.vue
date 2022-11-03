<template>
  <div style="position:relative; border:solid 1px blue;">
    <canvas ref="canvas" style="width:100%;"></canvas>
    <template v-if="engine">
      <slot />
    </template>
  </div>
</template>

<script>
  import * as THREE from 'three';
  import { AmmoPhysics, ExtendedMesh, PhysicsLoader } from '@enable3d/ammo-physics';

  export default {
    data() {
      return {
        width: false,
        height: false,
        camera: false,
        scene: false,
        light: false,
        renderer: false,
        canvas: false,
      };
    },
    methods: {
      threejsInit() {
        PhysicsLoader('/ammo', () => {
          console.log('start');
        });

        const threejsAnimation = () => {
          this.parentCall('onUpdate');
          requestAnimationFrame(threejsAnimation);
          this.renderer.render(this.scene, this.camera);
        };

        this.width = this.$el.offsetWidth;
        this.height = this.$el.offsetHeight;
        this.camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 0.01, 10 );
        
        this.scene = new THREE.Scene();
        this.scene.background = new THREE.Color(0xf0f0f0);

        this.light = new THREE.AmbientLight(0xffffff);
        this.scene.add(this.light);

        // const geometry = new THREE.BoxGeometry( 0.2, 0.2, 0.2 );
        // const material = new THREE.MeshNormalMaterial();
        // const mesh = new THREE.Mesh( geometry, material );
        // this.scene.add( mesh );

        this.renderer = new THREE.WebGLRenderer( { antialias: true } );
        this.renderer.setSize(this.width, this.height);
        this.renderer.setAnimationLoop(threejsAnimation);
        this.$refs.canvas.replaceWith(this.renderer.domElement);
        this.canvas = this.$refs.canvas;

        window.addEventListener('resize', () => {
          this.width = this.$el.offsetWidth;
          this.height = this.$el.offsetHeight;
          this.camera.aspect = this.width / this.height;
          this.camera.updateProjectionMatrix();
          this.renderer.setSize(this.width, this.height);
        });

        this.parentCall('onCreate');
      },
      parentCall(methodName, params = {}) {
        if (! typeof this.$parent[ methodName ] == 'function') return;
        this.$parent[ methodName ]({
          width: this.width,
          height: this.height,
          camera: this.camera,
          scene: this.scene,
          light: this.light,
          renderer: this.renderer,
          canvas: this.canvas,
          THREE,
          ...params
        });
      },
    },
    mounted() {
      this.threejsInit();
    },
  };
</script>