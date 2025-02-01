<script setup>
import { PlusIcon } from '@heroicons/vue/24/outline';

import { ref } from 'vue';
const showInputField = ref(false);
</script>

<template>
  <button
    className="absolute top-6 right-6 flex gap-2 capitalize bg-green-500 hover:bg-green-600 rounded-full items-center py-2 px-4 text-neutral-50 text-lg shadow-md transition cursor-pointer"
  >
    <PlusIcon class="size-5" />
    Add new
  </button>
</template>
