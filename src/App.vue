<template>
    <div class="w-[100vw] h-[100vh]">
        <div class="grid place-items-center">
            <div
                class="drag-zone"
                @click="passClick"
                @dragover="handleDragOver"
                @drop="handleDrop"
            >
                Drag &amp; Drop
            </div>
        </div>
        <input
            id="input"
            class="hidden"
            type="file"
            accept="image/*"
            ref="inputFileRef"
            @change="handleChange"
        />
        <div class="flex items-center flex-col gap-10 cursor-pointer">
            <img class="max-w-[150px]" :src="config.src" @click="printAscii" />
            <pre v-html="config.pre"></pre>
        </div>
    </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import imgToAscii from "./utils/imgToAscii";
import confetti from "canvas-confetti";

const config = reactive({
    src: "",
    pre: "",
    changed: false,
});

const inputFileRef = ref();

function passClick() {
    inputFileRef.value.click();
}

// 미리보기 이미지 그려주는 곳
function printImg(file) {
    const reader = new FileReader();

    reader.readAsDataURL(file);

    reader.onload = function (event) {
        const img = new Image();
        img.src = event.target.result;

        img.onload = function () {
            const canvas = document.createElement("canvas");
            const ctx = canvas.getContext("2d");
            const maxImageSize = 200;

            let scale = 1;
            if (img.width > maxImageSize || img.height > maxImageSize) {
                scale = Math.min(
                    maxImageSize / img.width,
                    maxImageSize / img.height,
                );
            }

            canvas.width = img.width * scale;
            canvas.height = img.height * scale;

            ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
            config.src = canvas.toDataURL();

            config.changed = false;
            config.pre = "";
        };
    };
}

function handleDragOver(event) {
    event.preventDefault();
}

function handleDrop(event) {
    event.preventDefault();

    printImg(event.dataTransfer.files[0]);
}

function handleChange(event) {
    printImg(event.target.files[0]);
}

async function printAscii() {
    if (!config.changed) {
        const art = new imgToAscii(config.src);

        config.pre = await art.displayColor(null, true);

        config.changed = true;
    }

    confetti({
        particleCount: 150,
        spread: 60,
        origin: {
            x: 0.5,
            y: 0.7,
        },
    });
}
</script>

<style lang="postcss"></style>
