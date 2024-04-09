<script setup>
import { ref, defineProps, defineEmits } from 'vue';

// Define props coming from parent component
const props = defineProps({
  title: String,
  options: Array
});

// Define events that will be emitted to parent
const emit = defineEmits(['update']);

const selectedOption = ref(props.options[0]?.value || 0);

// Function called on v-on:input
function emitValueUpdate() {
  emit('update', { value: selectedOption.value, title: props.title });
}
</script>

<template>
  <form class="definition-input">
    <label class="input-title">{{ title }}: {{ selectedOption }}</label>
    <select v-model="selectedOption" class="dropdown" @change="emitValueUpdate">
      <option v-for="option in options" :key="option.label" :value="option.value">
        {{ option.label }}
      </option>
    </select>
  </form>
</template>

<style scoped>
.dropdown {
  height: 40px;
  margin: 10px 0 0;
  padding: 0 15px;
  border: 1px solid black;
  border-radius: 8px;
  font-family: Roboto Mono, monospace;
  font-size: medium;
  width: 100%;
  cursor: pointer;
  color: #1b314f;
}
</style>