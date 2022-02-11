<template>
  <div id="navbar">
    <div class="container">
        <a href="#projects">Projects</a>
        <a href="/resume.pdf" download="Resume.pdf">Resume</a>
        <a href="#contact">Contact</a>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({});
</script>

<style scoped>
#navbar {
  position: fixed;
  width: 100%;
  pointer-events: all;
  z-index: 10;
}

.container {
  float: right;
  margin: 30px;
}

a {
    text-decoration: none;
    color: white;
    border-bottom: 2px solid #3e3e3e;
    padding: 10px 20px;
    margin: 10px;
    transition: 400ms ease all;
}

a:hover {
    border-color: white;
}

a:focus {
  outline: solid #6f00ff;
  border-radius: 5px;
}

/* mobile */

@media only screen and (max-width: 600px) {
  #navbar {
    background: #1f1f1f;
  }

  .container {
    float: none;
    margin: 20px 0;
    text-align: center;
    box-shadow: 0 0 5px #1f1f1f;
  }
}
</style>