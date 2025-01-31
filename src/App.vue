<script setup>
import { ref } from 'vue'

const name = ref('Brayden')
</script>

<template>
  <div class="text-blue-300">Hello there {{ name }}</div>
</template>