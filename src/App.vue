<template>
  <three class="three" :projects="data" @select="select" />
  <div id="main">
    <nav-bar />
    <div class="content">
      <div class="main-container">
        <h1 id="name">Sankarsh Makam</h1>
        <h2>🏢 Student at University of Bristol</h2>
      </div>
    </div>
    <div id="projects" class="content">
      <div class="content-container">
        <h1>Projects</h1>
        <Project v-for="(project, i) in data" :key="i" :="project" :ref="`p${i}`" />
      </div>
    </div>
    <div id="contact" class="content">
      <div class="content-container">
        <h1>Contact</h1>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Three from './components/Three.vue';
import NavBar from './components/NavBar.vue';
import Project from './components/Project.vue';

import data from '../public/projects.json';

interface Project {
  title: string;
  image: string;
  date: number;
  link: string;
  description: string;
  technologies: string[];
}

export default defineComponent({
  components: {
    Three,
    NavBar,
    Project,
  },
  data() {
    return { data: data as Project[] };
  },
  methods: {
    select(i: number) {
      for (let j = 0; j < this.data.length; j++) {
        const p = (this.$refs[`p${j}`] as any).$el as HTMLElement;
        console.log(p, j);
        if (j === i) p.style.transform = 'scale(1)';
        else p.style.transform = 'scale(0)';
      }
    },
  }
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
  pointer-events: all;
}

h2 {
  font-weight: 500;
  font-size: 1.25rem;
  pointer-events: all;
}

p {
  max-width: 50%;
  font-size: 1.25rem;
  pointer-events: all;
}

#main {
  z-index: 10;
  position: absolute;
  width: 100vw;
  top: 0;
  left: 0;
  pointer-events: none;
}

.content {
  height: 100vh;
}

.main-container {
  padding: 7%;
}

.content-container {
  padding: 2% 7%;
}

#card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

/* mobile */
@media only screen and (max-width: 600px) {
  h1 {
    padding: 50px 3%;
  }
  .content .content-container {
    padding: 3%;
  }
  #projects .content-container {
    padding: 2% 3%;
  }
}
</style>
