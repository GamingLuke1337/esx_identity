<script setup>
import { onMounted, provide, ref } from "vue";
import Identity from "./components/Identity.vue";

const translations = ref({});
provide('translations', translations);

const policeLogo = ref('');
provide('policeLogo', policeLogo);

onMounted(() => {
    fetch("http://esx_identity/ready", {
        method: "POST",
        body: JSON.stringify({}),
    });

    const getLightOpacityColor = (color, opacity) => {
        const rgbMatch = color.match(/rgb\(\s*(\d+)\s*,\s*(\d+)\s*,\s*(\d+)\s*\)/);
        if (rgbMatch) {
            const r = parseInt(rgbMatch[1]);
            const g = parseInt(rgbMatch[2]);
            const b = parseInt(rgbMatch[3]);
            return `rgba(${r}, ${g}, ${b}, ${opacity})`;
        }
        return color;
    };

    window.addEventListener("message", (event) => {
        if (event.data.type === "enableui") {
            document.body.classList[event.data.enable ? "remove" : "add"]("none");
        } else if (event.data.type === "sendcolor") {
            document.body.style.setProperty("--color", event.data.color);
            document.body.style.setProperty("--light-color", getLightOpacityColor(event.data.color, 0.5));
            document.body.style.setProperty("--light-color1", getLightOpacityColor(event.data.color, 0.3));
            document.body.style.setProperty("--light-color2", getLightOpacityColor(event.data.color, 0.1));
        } else if (event.data.type === "sendtranslations") {
            translations.value = event.data.translations || {};
        } else if (event.data.type === "sendpolicelogo") {
            policeLogo.value = event.data.logo || '';
        }
    });
});
</script>

<template>
    <Identity />
</template>

<style scoped></style>
