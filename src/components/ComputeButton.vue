<template>
  <div id="app">
    <button class="compute-button"
    @mousedown="onButtonClick(true)"
    @mouseup="onButtonClick(false)"
    :disabled="isDisabled"
    >{{ titlec }}</button>
  </div>
</template>

<script setup>
import { ref } from "vue";

const emits = defineEmits(["toggleCompute"]);

const props = defineProps(["title", "isDisabled"]);
const titlec = ref(props.title);
const isActive = ref(false);


async function onButtonClick(state) {
  isActive.value = state
  emits("toggleCompute", isActive.value );
}
</script>

<style scoped>

button, .customUpload {
    width: 100%;
    font-family: 'Roboto Mono', monospace;
    background-color:  rgba(241, 152, 88, 0.909);
    border:  rgba(237, 136, 63, 0.909);
    color: white;
    font-weight: bold;
    padding: 10px 15px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
    border-radius: 10px;
    transition: all 0.1s ease-in-out;
    
  }
  
  
  button:hover, .customUpload:hover {
    background-color: rgba(237, 136, 63, 0.909);
    box-shadow: 3px 3px rgba(154, 65, 2, 0.909);
  }


</style>