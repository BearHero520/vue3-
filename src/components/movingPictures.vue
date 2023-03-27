<template>
    <div class="image-container" :class="state.dragging ? 'moveimg' : 'moveend'"
        :style="{ left: state.mouseX + 'px', top: state.mouseY + 'px' }">
        <img ref="image" id="imagePR" :src="src" />
    </div>
</template>

<script setup>
import { reactive, ref, onMounted, nextTick, watch } from 'vue';

const props = defineProps({
    src: {
        type: String,
        required: true
    },
    containerWidth: {
        type: Number,
        default: window.innerWidth
    },
    containerHeight: {
        type: Number,
        default: window.innerHeight
    },
    px: {
        type: Number,//设置边距
        default: 0
    }
})


const state = reactive({
    dragging: false,
    mouseX: 0,
    mouseY: 500,
    x: 0,
    y: 0
});
watch(() => state.dragging, (val) => {
    if (val) {
        document.querySelector('body').setAttribute("style", "overflow: hidden;");
    } else {
        document.querySelector('body').setAttribute("style", "overflow: 'auto';");
    }

})

const image = ref(null)

function onMouseStart(event) {
    state.dragging = true;
}

function onMouseMove(event) {
    event.stopPropagation();
    if (state.dragging) {
        state.mouseX = event.changedTouches[0].clientX - (Math.floor(state.x / 2))
        state.mouseY = event.changedTouches[0].clientY - (Math.floor(state.y / 2))
    }
}

function onMouseUp(event) {
    state.dragging = false;


    snapToEdge();
}

function snapToEdge() {
    if (window.innerWidth / 2 < state.mouseX) {
        state.mouseX = window.innerWidth - state.x - props.px
    } else {
        state.mouseX = 0 + props.px
    }
}





onMounted(() => {
    nextTick(() => {
        console.log(window.innerWidth, 'window.innerWidth');
        const myImage = document.getElementById('imagePR')

        myImage.addEventListener('touchstart', onMouseStart)
        myImage.addEventListener('touchmove', onMouseMove)
        myImage.addEventListener('touchend', onMouseUp);
        state.x = myImage.clientWidth;
        state.y = myImage.clientHeight;
        state.mouseX = window.innerWidth - state.x - props.px
    })

})
</script>

<style scoped>
.image-container {
    position: fixed;
    user-select: none;
    z-index: 900;
}

.image-container>img {
    height: inherit;
    width: inherit;
}

.moveimg {
    transform: scale(1.1);
    opacity: 1;
}

.moveend {
    transition: all 0.3s !important;
    opacity: 0.6;
}
</style>
