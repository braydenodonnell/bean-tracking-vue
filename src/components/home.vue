<script setup>
import { ref } from 'vue';

import AddNewBtn from './add-new-btn.vue';
import Card from './views/card.vue';
import Filter from './views/filter.vue';
import MakeCoffeeBtn from './make-coffee-btn.vue';
import SearchBar from './search-bar.vue';

const activeTab = ref('current');
const handleActiveTab = (tab) => {
  activeTab.value = tab;
};

const searchQuery = ref('');
const handleSearchInput = (value) => {
  searchQuery.value = value;
};
</script>

<template>
  <div class="flex flex-col gap-12 w-screen items-center">
    <MakeCoffeeBtn />
    <SearchBar @update-search="handleSearchInput" />
    <Filter @update:activeTab="handleActiveTab" />
    <Card :tab="activeTab" :searchQuery="searchQuery" />
    <AddNewBtn />
  </div>
</template>
