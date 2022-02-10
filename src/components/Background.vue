<template>
  <div id="bg">
    <Renderer ref="renderer" antialias resize="window">
      <Camera
        ref="camera"
        :position="{ x: 40, y: 15, z: 40 }"
        :look-at="{ x: 0, y: 0, z: 0 }"
      />
      <Scene ref="scene" background="#1f1f1f">
        <AmbientLight color="#ffffff" :intensity="0.8" />
        <Group v-for="i in 10" v-bind:key="i">
          <Sphere
            v-for="j in 10"
            v-bind:key="j"
            :position="{ x: i * 5 - 5, y: 0, z: j * 5 - 5 }"
            :radius="0.5"
          >
            <LambertMaterial color="#fbc31c" />
          </Sphere>
          <Group v-if="i > 0" v-bind:key="i">
            <Box
              v-for="j in 10"
              v-bind:key="j"
              :position="{ x: i * 5 - 2.5, y: 0, z: j * 5 - 5 }"
              :width="5"
              :height="0.1"
              :depth="0.1"
            >
              <LambertMaterial color="#3e3e3e" />
            </Box>
          </Group>
          <Group v-if="i > 0" v-bind:key="i">
            <Box
              v-for="j in 9"
              v-bind:key="j"
              :position="{ x: i * 5 - 5, y: 0, z: j * 5 - 3 }"
              :width="0.1"
              :height="0.1"
              :depth="5"
            >
              <LambertMaterial color="#3e3e3e" />
            </Box>
          </Group>
        </Group>
      </Scene>
    </Renderer>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import {
  Renderer,
  Scene,
  Camera,
  AmbientLight,
  LambertMaterial,
  Group,
  Sphere,
  Box,
} from 'troisjs';

export default defineComponent({
  components: {
    Renderer,
    Scene,
    Camera,
    AmbientLight,
    LambertMaterial,
    Group,
    Sphere,
    Box,
  },
  methods: {
    sin(i: number, j: number) {
      return 0;
    },
  },
});
</script>
