<script setup>
import { ref } from 'vue';

const active = ref('current');

const emit = defineEmits();

const tabs = ref([
  { id: 'all', label: 'All' },
  { id: 'current', label: 'Current' },
  { id: 'favorites', label: 'Favroites' },
]);

const handleTab = (tab) => {
  active.value = tab;
  emit('update:activeTab', tab);
};
</script>

<template>
  <nav class="flex gap-4">
    <button
      v-for="tab in tabs"
      :key="tab.id"
      class="bg-neutral-100 border-2 px-4 py-1 rounded-full cursor-pointer transition-all duration-300"
      :class="{
        'border-blue-400 scale-105': active === tab.id,
        'border-neutral-300': active !== tab.id,
        'hover:bg-neutral-200': active !== tab.id,
      }"
      @click="handleTab(tab.id)"
    >
      {{ tab.label }}
    </button>
  </nav>
</template>
