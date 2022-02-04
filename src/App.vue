<template>
  <nav-bar />
  <three />
  <div>
    <h1>Sankarsh Makam</h1>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Three from "./components/Three.vue";
import NavBar from "./components/NavBar.vue";
export default defineComponent({
  components: {
    Three,
    NavBar,
  },
});
</script>

<style>
body {
  color: white;
  font-family: 'Open Sans', sans-serif;
}
</style>

<style scoped>
h1 {
  text-transform: lowercase;
  font-weight: 700;
  font-size: 5rem;
  width: 1px;
  padding: 7%;
}

h1::after {
  content: ".";
}

/* mobile */
@media only screen and (max-width: 600px) {
  h1 {
    padding: 50px 3%;
  }
}
</style>
