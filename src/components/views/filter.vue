<script setup>
import { ref } from 'vue';

const active = ref('all');

const tabs = ref([
  { id: 'all', label: 'All' },
  { id: 'current', label: 'Current' },
  { id: 'favorite', label: 'Favroite' },
]);

const setFavorite = (tab) => (active.value = tab);

// eventually, use watch to fetch data depending on which tab is active
</script>

<template>
  <nav class="flex gap-4">
    <button
      v-for="tab in tabs"
      :key="tab.id"
      class="bg-neutral-100 border-2 px-4 py-1 rounded-full cursor-pointer transition-all duration-300 hover:bg-neutral-200"
      :class="{
        'border-blue-400 scale-105': active === tab.id,
        'border-neutral-300': active !== tab.id,
      }"
      @click="setFavorite(tab.id)"
    >
      {{ tab.label }}
    </button>
  </nav>
</template>
