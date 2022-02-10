<template>
  <div id="navbar">
    <div class="container">
        <a href="#projects">Projects</a>
        <a href="#">Resume</a>
        <a href="#">Contact</a>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  components: {},
});
</script>

<style scoped>
#navbar {
  position: fixed;
  width: 100%;
  pointer-events: all;
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
</style>