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
]);
</script>

<template>
  <div class="grid grid-cols-3 gap-12">
    <!-- Loop over data to display cards -->
    <CardInformation />
  </div>
</template>
