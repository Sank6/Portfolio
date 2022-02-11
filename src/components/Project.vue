<template>
    <a class="project" :href="link" target="_blank">
        <img :src="image" :alt="`Demo for ${title}`" />
        <div class="right">
            <h2>{{ title }}</h2>
            <p class="date">{{(new Date(date)).toLocaleDateString(undefined, {month: "long", year: "numeric"})}}</p>
            <p class="description">{{ description }}</p>
        </div>
    </a>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

interface Project {
  title: string;
  image: string;
  date: number;
  link: string;
  description: string;
  technologies: string[];
}

export default defineComponent({
    props: {
        title: {
            type: String,
            required: true,
        },
        image: {
            type: String,
            required: true,
        },
        date: {
            type: Number,
            required: true,
        },
        link: {
            type: String,
            required: true,
        },
        description: {
            type: String,
            required: true,
        },
        technologies: {
            type: Array as () => string[],
            required: true
        }
    }
})
</script>

<style scoped>
.project {
    position: absolute;
    display: block;
    left: 0;
    margin: 7%;
    background: #1a1a1a;
    padding: 20px;
    margin-top: 100px;
    pointer-events: all;
    color: white;
    text-decoration: none;
    transition: 300ms ease all;
    transform: scale(0);
    width: 85%;
}

.project:hover {
    transform: scale(1.05) !important;
}

h2 {
    font-size: 1.5em;
    margin: 0;
}

.right {
    float: right;
    width: calc(50% - 60px);
    padding: 0 30px;
}

.date {
    opacity: 0.4;
    user-select: none;
}

.description {
    font-size: 1.1em;
    line-height: 1.2em;
}

img {
    width: 50%;
    filter: opacity(0.5);
    max-height: 40vh;
    object-fit: cover;
    transition: 400ms ease all;
}

.project:hover > img {
    filter: opacity(1)
}
</style>