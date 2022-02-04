<template>
    <div class="container">
        <a href="#">About</a>
        <a href="#">Projects</a>
        <a href="#">Contact</a>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  components: {},
});
</script>

<style scoped>
.container {
  float: right;
  margin: -10px 10px;
}

a {
    text-decoration: none;
    color: white;
    border: 2px solid #4c4c4c;
    padding: 10px 20px;
    margin: 10px;
    transition: 400ms ease all;
}

a:hover {
    background-color: #4c4c4c;
}
</style>