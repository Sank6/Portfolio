<template>
  <three class="three" />
  <div id="main">
    <nav-bar />
    <div class="content">
      <div>
        <h1 id="name">Sankarsh Makam</h1>
        <h2>🏢 University of Bristol</h2>
      </div>
    </div>
    <div id="projects" class="content">
      <div>
        <h1>Projects</h1>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Three from './components/Three.vue';
import NavBar from './components/NavBar.vue';
export default defineComponent({
  components: {
    Three,
    NavBar,
  },
});
</script>

<style>
html,
body {
  background: #1f1f1f;
  color: white;
  font-family: 'Open Sans', sans-serif;
  width: 100vw;
  margin: 0;
  overflow-x: hidden;
  scroll-behavior: smooth;
}
</style>

<style scoped>
#name {
  text-transform: lowercase;
  width: 1px;
}

#name::after {
  content: '.';
}

h1 {
  font-weight: 700;
  font-size: 5rem;
}

h2 {
  font-weight: 700;
  font-size: 1.75rem;
}

p {
  max-width: 50%;
  font-size: 1.25rem;
}

#main {
  z-index: 10;
  position: absolute;
  width: 100vw;
  top: 0;
  left: 0;
}

.content {
  height: 100vh;
}

.content div {
  
  padding: 7%;
}

/* mobile */
@media only screen and (max-width: 600px) {
  h1 {
    padding: 50px 3%;
  }
}
</style>
