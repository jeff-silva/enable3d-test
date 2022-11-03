<template>
  <div style="position:relative; border:solid 1px blue;">
    <canvas ref="canvas" style="width:100%;"></canvas>
    <template v-if="engine">
      <slot />
    </template>
  </div>
</template>

<script>
  import * as BABYLON from 'babylonjs';

  export default {
    data() {
      return {
        canvas: false,
        engine: false,
        scene: false,
        camera: false,
        light: false,
      };
    },
    methods: {
      babylonjsInit() {

        // Canvas
        this.canvas = this.$refs.canvas;

        // Engine
        this.engine = this.engine || new BABYLON.Engine(this.canvas, true, {preserveDrawingBuffer: true, stencil: true});
        this.scene = this.scene || new BABYLON.Scene(this.engine);
        
        this.camera = this.camera || new BABYLON.FreeCamera('camera1', new BABYLON.Vector3(0, 5, -10), this.scene);
        this.camera.attachControl(this.canvas, false);

        this.light = this.light || new BABYLON.HemisphericLight('light1', new BABYLON.Vector3(0, 1, 0), this.scene);

        // let sphere = BABYLON.Mesh.CreateSphere('sphere1', 16, 2, this.scene, false, BABYLON.Mesh.FRONTSIDE);
        // let ground = BABYLON.Mesh.CreateGround('ground1', 6, 6, 2, this.scene, false);

        this.parentCall('onCreate');

        this.engine.runRenderLoop(() => {
          this.parentCall('onUpdate');
          this.scene.render();
        });

        window.addEventListener('resize', () => {
          this.engine.resize();
        });
      },
      parentCall(methodName, params = {}) {
        if (! typeof this.$parent[ methodName ] == 'function') return;
        this.$parent[ methodName ]({
          canvas: this.canvas,
          engine: this.engine,
          scene: this.scene,
          camera: this.camera,
          light: this.light,
          BABYLON,
          ...params
        });
      },
    },
    mounted() {
      this.babylonjsInit();
    },
  };
</script>