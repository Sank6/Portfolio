<template>
    <a class="project" :href="link" target="_blank">
        <img :src="image" :alt="`Demo for ${title}`" />
        <div class="right">
            <h2>{{ title }}</h2>
            <p class="date">{{(new Date(date)).toLocaleDateString(undefined, {month: "long", year: "numeric"})}}</p>
            <div id="technologies">
                <span class="icon" v-for="t in technologies" :key="t">
                    <oh-vue-icon :alt="t" :name="`si-${t.toLowerCase().replace('.', 'dot')}`" />
                    <span class="text">{{ t }}</span>
                </span>
            </div>
            <p class="description">{{ description }}</p>
        </div>
    </a>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { OhVueIcon, addIcons } from "oh-vue-icons";
import * as SiIcons from 'oh-vue-icons/icons/si';

export default defineComponent({
    components: {
        OhVueIcon,
    },
    beforeMount() {
        addIcons(...Object.values({ ...SiIcons }) as any);
    },
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
    width: calc(85% - 40px);
}

.project:hover, .project:focus {
    transform: scale(1.02) !important;
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

#technologies {
    margin-top: 30px;
}

.icon svg {
    width: 30px;
    height: 30px;
    margin: 5px 0;
    opacity: 0.3;
    color: #fff;
    transition: 400ms ease all;
}

.icon:hover svg {
    opacity: 1;
    color: #fbc31c
}

/* Tooltips */

.icon {
    position: relative;
}

.text {
  visibility: hidden;
  opacity: 0;
  background-color: #ebebeb;
  color: #1f1f1f;
  text-align: center;
  border-radius: 6px;
  position: absolute;
  z-index: 1;
  bottom: 220%;
  width: 65px;
  left: 0;
  transform: translate(-17px, 0);
  font-size: 0.8rem;
  transition: 400ms ease all;
}

.text::after {
    content: "";
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -5px;
    border-width: 5px;
    border-style: solid;
    border-color: #ebebeb transparent transparent transparent;
}

.icon:hover .text {
    visibility: visible;
    opacity: 1;
}

@media only screen and (max-width: 600px) {
    .project {
        position: static;
        opacity: 1;
        transform: scale(1);
        margin-top: 50px;
    }

    h2 {
        margin-top: 20px;
    }

    img {
        width: 100%;
        max-height: 20vh;
        filter: opacity(1);
    }

    .right {
        float: none;
        width: 100%;
        padding: 0;
    }
}
</style>