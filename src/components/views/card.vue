<script setup>
import { onMounted, ref, watch } from 'vue';
import CardInformation from '../card-information.vue';

import { supabase } from '../../lib/supabase-client';

const beanData = ref([]);

async function fetchData() {
  const { data, error } = await supabase
    .from('coffee_beans')
    .select()
    .order('id');

  beanData.value = data;
}

onMounted(() => {
  fetchData();
});

watch(beanData, (newBeanData) => {
  if (newBeanData) {
    fetchData();
  }
});
</script>

<template>
  <div
    class="grid grid-cols-1 gap-12 mb-16 sm:grid-cols-2"
    :class="{
      'lg:grid-cols-3': beanData.length >= 3,
      'lg:grid-cols-2': beanData.length === 2,
      'lg:grid-cols-1': beanData.length === 1,
    }"
  >
    <CardInformation
      v-for="coffee in beanData"
      :key="coffee.id"
      :data="coffee"
    />
  </div>
</template>
