<script setup>
import { ref } from 'vue'

const number = ref(0)
const increament = () => number.value++
defineExpose({number, increament})
</script>

<template>
  <p>{{ number }}</p>
  <button @click="increament()">increament</button>
</template>