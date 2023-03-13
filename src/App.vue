<template>
    <div class="container">
        <p>이미지를 아스키 아트로 바꿔줘요!</p>
        <input type="file" @change="handleChange" />
        <img :src="config.src" @click="printImg" />
        <pre v-html="config.pre"></pre>
    </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import imgToAscii from "./imgToAscii";

const config = reactive({
    src: "",
    container: ref(),
    pre: "",
});

function handleChange(event) {
    const { target } = event;

    const reader = new FileReader();

    reader.onload = function () {
        config.src = reader.result;
    };

    reader.readAsDataURL(target.files[0]);
}

async function printImg() {
    const art = new imgToAscii(config.src);

    config.pre = await art.displayColor(null, true);
    console.log(config.pre);

    // config.pre = await art.display();
}
</script>

<style lang="postcss">
.container {
    text-align: center;
}
</style>
