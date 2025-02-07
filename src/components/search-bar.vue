<script setup>
import { ref } from 'vue';

import { MagnifyingGlassIcon } from '@heroicons/vue/24/outline';

const searchQuery = ref('');

const emit = defineEmits(['update-search']);

const handleSearchInput = () => emit('update-search', searchQuery.value);
</script>

<template>
  <div
    class="flex items-center gap-3 w-96 mt-6 rounded-full px-4 py-1.5 border-2 border-neutral-300 bg-neutral-200 text-neutral-600 text-lg"
  >
    <MagnifyingGlassIcon class="size-6 text-neutral-500" />
    <input
      class="w-full focus:outline-none"
      placeholder="Search by origin, notes, ...anything!"
      v-model="searchQuery"
      @input="handleSearchInput"
    />
  </div>
</template>
