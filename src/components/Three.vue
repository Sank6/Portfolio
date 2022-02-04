<template>
  <div id="bg">
    <Renderer ref="renderer" antialias resize="window">
      <Camera
        ref="camera"
        :position="{ x: 160, y: 200, z: 160 }"
        :look-at="{ x: 60, y: 120, z: 20 }"
        :far="2000"
      />
      <Scene ref="scene" background="#2c2c2c">
        <AmbientLight color="#ffffff" :intensity="0.8" />

        <InstancedMesh ref="tubesZ" :count="n * n">
          <BoxGeometry :width="0.5" :height="0.5" :depth="n * 3" />
          <LambertMaterial />
        </InstancedMesh>
        <InstancedMesh ref="tubesY" :count="n * n">
          <BoxGeometry :width="0.5" :height="0.5" :depth="n * 3" />
          <LambertMaterial />
        </InstancedMesh>
        <InstancedMesh ref="spheres" :count="n ** 2">
          <SphereGeometry :radius="2" />
          <LambertMaterial />
        </InstancedMesh>
        <!--
        <Sphere ref="mainmesh" :radius="40" :position="{ x: 120, y: 120, z: 20 }" :widthSegments="20" :heightSegments="20">
            <BasicMaterial color="#6f00ff" :props="{ wireframe: true, transparent: true, opacity: 1 }" />
        </Sphere>
        -->
      </Scene>
    </Renderer>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { Object3D, Color, SphereGeometry as SG, EdgesGeometry, LineSegments, LineBasicMaterial } from 'three';
import {
  Camera,
  Renderer,
  RendererPublicInterface,
  Scene,
  InstancedMesh,
  SphereGeometry,
  BoxGeometry,
  LambertMaterial,
  AmbientLight,
} from 'troisjs';

export default defineComponent({
  components: {
    Camera,
    Renderer,
    Scene,
    InstancedMesh,
    SphereGeometry,
    BoxGeometry,
    LambertMaterial,
    AmbientLight,
  },
  setup() {
    return {
      spheres: null as typeof InstancedMesh.mesh | null,
      tubesZ: null as typeof InstancedMesh.mesh | null,
      tubesY: null as typeof InstancedMesh.mesh | null,
      renderer: null as RendererPublicInterface | null,
      scene: null as typeof Scene.scene | null,
      camera: null as typeof Camera.camera | null,
      line: null as LineSegments<EdgesGeometry, LineBasicMaterial> | null,
      diff: 0,
      n: 15,
    };
  },
  mounted() {
    this.spheres = (this.$refs.spheres as typeof InstancedMesh).mesh;
    this.tubesZ = (this.$refs.tubesZ as typeof InstancedMesh).mesh;
    this.tubesY = (this.$refs.tubesY as typeof InstancedMesh).mesh;
    this.renderer = this.$refs.renderer as RendererPublicInterface;
    this.renderer.renderer.setClearColor(new Color(0x050505));
    this.scene = this.$refs.scene as typeof Scene.scene;
    this.camera = this.$refs.camera as typeof Camera.camera;

    const geometry = new SG(40, 25, 25);
    const edges = new EdgesGeometry( geometry );
    this.line = new LineSegments( edges, new LineBasicMaterial( { color: 0x6f00ff, transparent: true, opacity: 1 } ) );
    this.line.position.set(120, 120, 20);
    this.scene.scene.add(this.line);

    this.renderer.onBeforeRender(this.animate);
  },
  methods: {
    animate() {
      // Draw spheres
      let x0 = -this.n * 14 * 1.5;
      let intensifier = 5;
      let waves = 2;
      const dummy = new Object3D();
      for (let x = 0; x < this.n; x++) {
        let xs = [];
        for (let z = 0; z < this.n; z++) {
          let y = Math.sin(((x + z) / 30) * 2 * waves * Math.PI + this.diff);
          y *= intensifier;
          dummy.position.set(x0 + x * this.n * 3, y, x0 + z * this.n * 3);
          dummy.updateMatrix();
          this.spheres?.setMatrixAt(this.n * x + z, dummy.matrix);
          this.spheres?.setColorAt(this.n * x + z, new Color(0xfbc31c));
        }
      }
      if (this.spheres) this.spheres.instanceMatrix.needsUpdate = true;

      // Draw tubes Z
      let pointA = new Object3D(),
        pointB = new Object3D();
      for (let x = 0; x < this.n; x++) {
        for (let z = 0; z < this.n - 1; z++) {
          this.spheres?.getMatrixAt(this.n * x + z, pointA.matrix);
          this.spheres?.getMatrixAt(this.n * x + z + 1, pointB.matrix);

          pointA.position.setFromMatrixPosition(pointA.matrix);
          pointB.position.setFromMatrixPosition(pointB.matrix);

          dummy.position.lerpVectors(pointA.position, pointB.position, 0.5);
          dummy.lookAt(pointA.position);

          dummy.updateMatrix();
          this.tubesZ?.setMatrixAt(15 * x + z, dummy.matrix);
          this.tubesZ?.setColorAt(15 * x + z, new Color(0x4c4c4c));
        }
      }
      if (this.tubesZ) this.tubesZ.instanceMatrix.needsUpdate = true;
      for (let x = 0; x <= this.n - 1; x++) {
        for (let z = 0; z < this.n; z++) {
          this.spheres?.getMatrixAt(this.n * x + z, pointA.matrix);
          this.spheres?.getMatrixAt(this.n * (x + 1) + z, pointB.matrix);

          pointA.position.setFromMatrixPosition(pointA.matrix);
          pointB.position.setFromMatrixPosition(pointB.matrix);

          dummy.position.lerpVectors(pointA.position, pointB.position, 0.5);
          dummy.lookAt(pointB.position);

          dummy.updateMatrix();
          this.tubesY?.setMatrixAt(15 * x + z, dummy.matrix);
          this.tubesY?.setColorAt(15 * x + z, new Color(0x4c4c4c));
        }
      }
      if (this.tubesY) this.tubesY.instanceMatrix.needsUpdate = true;

      this.diff = (this.diff + 0.02) % (2 * Math.PI);

      // spin the mesh
      if (this.line) this.line.rotation.y += 0.005;
    },
  },
});
</script>

<style>
body,
html {
  margin: 0;
}
canvas {
  display: block;
}
#bg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
}
</style>
