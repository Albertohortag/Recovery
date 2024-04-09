<template>
  <div id="app">
    <button v-on:click="onButtonClick">{{ titlec }}</button>
  </div>
</template>

<script setup>
import { ref } from "vue";

const emits = defineEmits(["toggleCompute"]);

const props = defineProps(["title"]);
const titlec = ref(props.title);

async function onButtonClick() {
  emits("toggleCompute", 1);
}
</script>
