<script setup>
const props = defineProps(['brand', 'name']);
import { ref } from 'vue';
import CardInformation from '../card-information.vue';

const data = ref([
  {
    brand: 'aldea',
    name: 'espresso blend',
  },
  {
    brand: 'coffee bros',
    name: 'brazil ip',
  },
  {
    brand: 'aldea',
    name: 'decaf',
  },
  {
    brand: 'coffee bros',
    name: 'Araku',
  },
]);
</script>

<template>
  <div
    class="grid grid-cols-1 gap-12 mb-16"
    :class="{
      'grid-cols-3': data.length >= 3,
      'grid-cols-2': data.length === 2,
    }"
  >
    <!-- Loop over data to display cards -->
    <CardInformation
      v-for="coffee in data"
      :key="coffee.name"
      :brand="coffee.brand"
      :name="coffee.name"
    />
  </div>
</template>
