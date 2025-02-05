<script setup>
import { ref, watch } from 'vue';

import AddNewBtn from './add-new-btn.vue';
import Card from './views/card.vue';
import Filter from './views/filter.vue';

const activeTab = ref('all');

const handleActiveTab = (tab) => {
  activeTab.value = tab;
};
</script>

<template>
  <div class="flex flex-col gap-20 w-screen items-center">
    <Filter @update:activeTab="handleActiveTab" />
    <Card :tab="activeTab" />
    <AddNewBtn />
  </div>
</template>
