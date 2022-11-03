<template>
  <app-threejs>
    <div>Hello</div>
  </app-threejs>
</template>

<script>
  export default {
    data() {
      return {
        size: [],
      };
    },
    methods: {
      onCreate({ THREE, scene }) {

        // const geometry = new THREE.BoxGeometry( 0.2, 0.2, 0.2 );
        // const material = new THREE.MeshNormalMaterial();
        // const mesh = new THREE.Mesh( geometry, material );
        // scene.add( mesh );

        // console.log('game:onCreate');
      },
      onUpdate() {

        // console.log('game:onUpdate');
        // this.camera.lookAt(this.sphere.absolutePosition);
      },
    },
  };
</script>