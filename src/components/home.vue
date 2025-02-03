<script setup>
import AddNewBtn from './add-new-btn.vue';
import Card from './views/card.vue';
import Filter from './views/filter.vue';
</script>

<template>
  <div class="flex flex-col gap-20 w-screen items-center">
    <Filter />
    <Card />
    <AddNewBtn />
  </div>
</template>
