<template>
  <three class="three" ref="three" :projects="data" @select="select" />
  <div id="main">
    <nav-bar />
    <div id="me" class="content">
      <div class="main-container">
        <h1 id="name">Sankarsh Makam</h1>
        <h2>🏢 Student at University of Bristol</h2>
      </div>
    </div>
    <div id="projects" class="content">
      <div class="content-container">
        <h1>Projects</h1>
        <Project v-for="(project, i) in data" :key="i" :="project" :ref="`p${i}`" @focus="focus(i)" />
        <div class="navigate-btn left">
          <oh-vue-icon name="oi-chevron-left" @click="prev" />
        </div>
        <div class="navigate-btn right">
          <oh-vue-icon name="oi-chevron-right" @click="next" />
        </div>
      </div>
    </div>
    <div id="contact" class="content">
      <div class="content-container">
        <h1>Contact</h1>
        <div id="links-container">
          <a class="icon" v-for="{name, link} in contact" :key="name" :href="link" @focus="contactFocus">
            <oh-vue-icon :name="name" />
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, defineAsyncComponent } from 'vue';
import NavBar from './components/NavBar.vue';
import Project from './components/Project.vue';

import data from '../public/projects.json';

import { OhVueIcon, addIcons } from "oh-vue-icons";
import { SiGithub, SiLinkedin, SiKeybase, OiMail, OiChevronLeft, OiChevronRight  } from 'oh-vue-icons/icons';

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
    Three: defineAsyncComponent(() => import('./components/Three.vue')),
    NavBar,
    Project,
    OhVueIcon,
  },
  data() {
    return { 
      data: data as Project[],
      contact: [
        {
          name: "si-github",
          link: "https://github.com/Sank6",
        },
        {
          name: "si-keybase",
          link: "https://keybase.io/Sank6",
        },
        {
          name: "si-linkedin",
          link: "https://www.linkedin.com/in/sankarshm",
        },
        {
          name: "oi-mail",
          link: "mailto:sankarshm@yahoo.com",
        }
      ],
      selected: 0,
    };
  },
  beforeMount() {
    addIcons(...Object.values({ SiGithub, SiLinkedin, SiKeybase, OiMail, OiChevronLeft, OiChevronRight }) as any);
  },
  methods: {
    select(i: any) {
      this.selected = i as number;
      if (window.innerWidth < 600) return;
      for (let j = 0; j < this.data.length; j++) {
        const p = (this.$refs[`p${j}`] as any).$el as HTMLElement;
        if (j === i) p.style.transform = 'scale(1)';
        else p.style.transform = 'scale(0)';
      }
    },
    focus(i: number) {
      if (window.innerWidth < 600) return;
      this.select(i);
      (this.$refs.three as any).click(i+1);
      document.querySelector("#projects")?.scrollIntoView();
    },
    contactFocus() {
      if (window.innerWidth < 600) return;
      document.querySelector("#contact")?.scrollIntoView();
    },
    prev() {
      document.querySelector("#projects")?.scrollIntoView();
      let newSelected = (this.selected - 1) % this.data.length;
      if (newSelected < 0) newSelected = this.data.length - 1;
      (this.$refs.three as any).click(newSelected+1);
      this.select(newSelected);
    },
    next() {
      document.querySelector("#projects")?.scrollIntoView();
      let newSelected = (this.selected + 1) % this.data.length;
      (this.$refs.three as any).click(newSelected+1);
      this.select(newSelected);
    }
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

a:focus {
  outline: solid #6f00ff;
  border-radius: 5px;
}

#main {
  z-index: 10;
  position: absolute;
  width: 100vw;
  top: 0;
  left: 0;
  pointer-events: none;
}

#me, #contact {
  height: 100vh;
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

#links-container {
  pointer-events: all;
  display: flex;
  height: 50vh;
  justify-content: center;
  align-items: center;
}

.icon {
  padding: 5px;
  margin: 10px;
  width: 70px;
  transform: translateY(0px);
  transition: 400ms ease all;
}

.icon:hover {
  transform: translateY(-10px);
}

.icon svg {
  color: #ffffff;
  width: 70px;
  height: 70px;
  filter: drop-shadow(0px 0px 2px #ffffff);
  transition: 400ms ease all;
}

.icon:hover svg {
  color: #fbc31c;
  filter: drop-shadow(0px 0px 2px #fbc31c);
}

.navigate-btn {
  color: white;
  position: absolute;
  border-radius: 50%;
  background-color: #2c2c2c;
  box-shadow: 0 0 10px #2c2c2c;
  pointer-events: all;
  transform: scale(1);
  transition: 400ms ease all;
}

.navigate-btn:hover {
    transform: scale(1.2) !important;
    color: #fbc31c;
    cursor: pointer;
}

.navigate-btn svg {
  width: 50px;
  height: 50px;
}

.left {
  margin: calc(100px + 20vh) 0 0 -25px;
}

.right {
  margin: calc(100px + 20vh) 0 0 calc(85% - 25px); 
}

/* mobile */
@media only screen and (max-width: 600px) {
  h1 {
    padding: 50px 3%;
  }

  .main-container {
    padding: 2%;
  }

  .content .content-container {
    padding: 3%;
  }

  #projects {
    opacity: 1 !important;
  }

  #projects .content-container {
    padding: 2% 3%;
  }

  .icon {
    display: block;
  }

  .icon svg {
    color: white;
    filter: drop-shadow(0px 0px 2px white);
  }

  .content {
    height: auto;
  }

  #links-container {
    display: block !important;
    margin: 0 auto;
    width: 100px;
    text-align: center;
  }

  .navigate-btn {
    display: none;
  }
}
</style>
