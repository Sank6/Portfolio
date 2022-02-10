<template>
  <div id="bg">
    <Renderer ref="renderer" antialias resize="window" >
      <Camera
        ref="camera"
        :position="{ x: 160, y: 200, z: 160 }"
        :look-at="{ x: 0, y: 0, z: 0 }"
        :far="2000"
      />
      <Scene ref="scene" background="#1f1f1f">
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
        <Box ref="box" :width="0.5" :height="0.5" :depth="1000">
          <BasicMaterial color="#3e3e3e" :props="{ transparent: true, opacity: 0 }" />
        </Box>
        <Sphere :radius="2" v-for="i in projects.length" v-bind:key="i" :ref="`dot-${i}`" @pointerEnter="enter(i)" @pointerLeave="leave(i)">
          <BasicMaterial color="#fbc31c" :props="{ transparent: true, opacity: 0 }" />
        </Sphere>
        <GltfModel src="/klein_bottle.gltf" @load="onload" />
      </Scene>
    </Renderer>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import {
  Object3D,
  Color,
  EdgesGeometry,
  LineSegments,
  LineBasicMaterial,
  Vector3,
  Euler,
  Vector2,
  Raycaster,
} from 'three';
import {
  Camera,
  Renderer,
  RendererPublicInterface,
  Scene,
  InstancedMesh,
  Sphere,
  SphereGeometry,
  Box,
  BoxGeometry,
  BasicMaterial,
  LambertMaterial,
  AmbientLight,
  GltfModel,
} from 'troisjs';

interface Project {
  title: string;
  image: string;
  date: number;
  link: string;
  description: string;
  technologies: string;
}

export default defineComponent({
  components: {
    Camera,
    Renderer,
    Scene,
    InstancedMesh,
    Sphere,
    SphereGeometry,
    Box,
    BoxGeometry,
    BasicMaterial,
    LambertMaterial,
    AmbientLight,
    GltfModel,
  },
  props: {
    projects: {
      type: Array as () => Project[],
      required: true,
    },
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
      box: null as typeof Box | null,
      dots: [] as typeof Sphere[],
      hideTubes: false,
      cameraPos: {
        start: new Vector3(160, 200, 160),
        end: new Vector3(150, 200, 150),
      },
      cameraRotation: {
        start: new Vector3(0, 0, 0),
        end: new Vector3(-1, 0, 0),
      },
      diff: 0,
      lineRotation: 0,
      n: 15,
      scrollY: window.scrollY,
      scrollFrac: 0,
      projectDots: [new Vector3(0, 0, 0)],
      pointer: new Vector2(0, 0),
      raycaster: new Raycaster(),
      firstDraw: 0,
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
    this.box = this.$refs.box as typeof Box;

    for (let i = 1; i <= this.projects.length; i++) {
      if (this.$refs[`dot-${i}`]) this.dots.push(this.$refs[`dot-${i}`] as typeof Sphere);
    }

    this.camera.camera.lookAt(new Vector3(60, 120, 20));
    this.cameraRotation.start.copy(this.camera.camera.rotation);
    this.scrollFrac = 1;
    this.updateScroll();
    
    this.renderer.onBeforeRender(this.animate);
    window.addEventListener('scroll', this.onScroll);
    window.addEventListener('resize', this.onResize);
    window.addEventListener( 'mousemove', this.onMove );
  },
  methods: {
    calculate() {
      if (this.firstDraw <= 1) return; 
      let dates = []
      for (let p of this.projects) dates.push(new Date(p.date));
      dates = dates.sort((a, b) => a.getTime() - b.getTime())
      let startYear = (new Date(String(dates[0].getFullYear()))).getTime();
      let endYear = (new Date(String(new Date().getFullYear()))).getTime();
      this.projectDots = [];
      for (let i in this.projects) {
        let p = this.projects[i];
        let frac = (new Date(p.date).getTime() - startYear) / (endYear - startYear);
        this.projectDots.push(new Vector3(frac * 300 , 0, -20));
        this.dots[i].mesh.position.copy(this.projectDots[i]);
      }
      this.box?.mesh.position.set(-50, 0, -20);
      this.box?.mesh.lookAt(this.projectDots[this.projectDots.length - 1]);
      this.onScroll();
    },
    animate() {
      if (!this.spheres) return;
      // Draw spheres
      let x0 = -this.n * 14 * 1.5;
      let intensifier = 5;
      let waves = 2;
      const dummy = new Object3D();
      for (let x = 0; x < this.n; x++) {
        for (let z = 0; z < this.n; z++) {
          let y = Math.sin(((x + z) / 30) * 2 * waves * Math.PI + this.diff);
          y *= intensifier;
          let startpos = new Vector3(x0 + x * this.n * 3, y, x0 + z * this.n * 3);
          let endpos = this.projectDots.sort((a, b) => a.distanceTo(startpos) - b.distanceTo(startpos))[0]
          let lerped = startpos.lerp(endpos, this.scrollFrac);
          dummy.position.copy(lerped);
          dummy.updateMatrix();
          this.spheres?.setMatrixAt(this.n * x + z, dummy.matrix);
          this.spheres?.setColorAt(this.n * x + z, new Color(0xfbc31c));
        }
      }
      if (this.spheres) this.spheres.instanceMatrix.needsUpdate = true;

      // Draw "tubes"
      let pointA = new Object3D(),
        pointB = new Object3D();
      for (let x = 0; x < this.n; x++) {
        for (let z = 0; z < this.n - 1; z++) {
          this.spheres?.getMatrixAt(this.n * x + z, pointA.matrix);
          this.spheres?.getMatrixAt(this.n * x + z + 1, pointB.matrix);

          pointA.position.setFromMatrixPosition(pointA.matrix);
          pointB.position.setFromMatrixPosition(pointB.matrix);

          dummy.position.lerpVectors(pointA.position, pointB.position, 0.5);
          if (!this.hideTubes) dummy.scale.set(1, 1, pointA.position.distanceTo(pointB.position) / (this.n * 3));
          else dummy.scale.set(0, 0, 0);
          dummy.lookAt(pointA.position);
   
          dummy.updateMatrix();
          this.tubesZ?.setMatrixAt(15 * x + z, dummy.matrix);
          this.tubesZ?.setColorAt(15 * x + z, new Color(0x3e3e3e));
        }
      }
      for (let x = 0; x <= this.n - 1; x++) {
        for (let z = 0; z < this.n; z++) {
          this.spheres?.getMatrixAt(this.n * x + z, pointA.matrix);
          this.spheres?.getMatrixAt(this.n * (x + 1) + z, pointB.matrix);

          pointA.position.setFromMatrixPosition(pointA.matrix);
          pointB.position.setFromMatrixPosition(pointB.matrix);

          dummy.position.lerpVectors(pointA.position, pointB.position, 0.5);
          if (!this.hideTubes) dummy.scale.set(1, 1, pointA.position.distanceTo(pointB.position) / (this.n * 3));
          else dummy.scale.set(0, 0, 0);
          dummy.lookAt(pointB.position);

          dummy.updateMatrix();
          this.tubesY?.setMatrixAt(15 * x + z, dummy.matrix);
          this.tubesY?.setColorAt(15 * x + z, new Color(0x3e3e3e));
        }
      }
      if (this.tubesZ) this.tubesZ.instanceMatrix.needsUpdate = true;
      if (this.tubesY) this.tubesY.instanceMatrix.needsUpdate = true;

      this.diff = (this.diff + 0.02) % (2 * Math.PI);

      this.lineRotation = (this.lineRotation + 0.01) % (2 * Math.PI);
      if (this.line) this.line.rotation.z = this.lineRotation;
      
      if (this.firstDraw < 2) {
        this.firstDraw ++;
        this.calculate();
      }
    },
    onload(model: any) {
      let scene = this.scene.scene;
      let self = this;
      model.traverse(function (child: any) {
        if (child.type === 'Mesh') {
          let edges = new EdgesGeometry(child.geometry);
          self.line = new LineSegments(
            edges,
            new LineBasicMaterial({
              color: 0x6f00ff,
              transparent: true,
              opacity: 1,
            })
          );
          // if mobile
          if (window.innerWidth < 600) self.line.position.set(60, 50, 20);
          else if (window.innerWidth < 1000)
            self.line.position.set(100, 100, 20);
          else self.line.position.set(120, 120, 20);
          self.line.scale.set(3, 3, 3);
          self.line.rotateX(Math.PI / 2);
          self.line.rotateY(Math.PI);
          scene.add(self.line);
          child.visible = false;
          self.onScroll();
        }
      });
    },
    onScroll() {
      if (this.scrollY < window.scrollY)
        this.lineRotation = (this.lineRotation + 0.05) % (2 * Math.PI);
      else if (this.scrollY > window.scrollY)
        this.lineRotation = (this.lineRotation - 0.1) % (2 * Math.PI);
      this.scrollY = window.scrollY;
      this.scrollFrac = Math.min(window.scrollY / window.innerHeight, 1);
      this.updateScroll();
    },
    updateScroll() {
      if (this.line) this.line.material.opacity = 1 - this.scrollFrac * 2;
      if (this.tubesY) this.tubesY.material.opacity = 1 - this.scrollFrac;
      if (this.tubesZ) this.tubesZ.material.opacity = 1 - this.scrollFrac;
      if (this.camera) {
        let lookAt = new Vector3(0, 0, 0);
        lookAt.lerpVectors(this.cameraRotation.start, this.cameraRotation.end, this.scrollFrac);
        this.camera.camera.rotation.copy(new Euler().setFromVector3(lookAt));
        this.camera.camera.updateProjectionMatrix();
      }
      if (this.scrollFrac > 0.9) this.hideTubes = true;
      else this.hideTubes = false;

      for (let d of this.dots) {
        d.mesh.material.opacity = (this.scrollFrac - 0.875) * 8;
        d.mesh.scale.set((this.scrollFrac - 0.5) * 2, (this.scrollFrac - 0.5) * 2, (this.scrollFrac - 0.5) * 2);
      }

      if (this.scrollFrac == 1) this.spheres.visible = false;
      else this.spheres.visible = true;

      if (this.box) {
        this.box.mesh.material.opacity = (this.scrollFrac - 0.5) * 2;
        this.box.mesh.scale.set((this.scrollFrac - 0.5) * 2, (this.scrollFrac - 0.5) * 2, (this.scrollFrac - 0.5) * 2);
      }

      if (window.innerWidth < 600) this.camera.camera.fov = 50 + 50 * this.scrollFrac;
    },
    onResize() {
      if (this.line) {
        if (window.innerWidth < 600) this.line.position.set(60, 80, 20);
        else if (window.innerWidth < 1000) this.line.position.set(100, 80, 20);
        else this.line.position.set(120, 100, 20);
      }
    },
    onMove(e: MouseEvent) {
				this.pointer.x = ( e.clientX / window.innerWidth ) * 2 - 1;
				this.pointer.y = - ( e.clientY / window.innerHeight ) * 2 + 1;
    },
    enter(i: number) {
      if (this.scrollFrac == 1) {
        this.dots[i - 1].mesh.material.color.set(0xff0000);
        this.dots[i-1].mesh.scale.set(1.5, 1.5, 1.5);
        document.body.style.cursor = "pointer";
      }
    },
    leave(i: number) {
      this.dots[i - 1].mesh.material.color.set(0xfbc31c);
      this.dots[i-1].mesh.scale.set(1, 1, 1);
      document.body.style.cursor = "auto";
    }
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
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}
</style>
