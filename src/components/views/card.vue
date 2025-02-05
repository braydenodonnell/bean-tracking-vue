<script setup>
import { onMounted, ref, watch, watchEffect } from 'vue';
import CardInformation from '../card-information.vue';

import { supabase } from '../../lib/supabase-client';

const beanData = ref([]);

const { tab } = defineProps(['tab']);

async function fetchData() {
  // all: all the coffee bean entries in supabase
  if (tab === 'all') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .order('id');

    return (beanData.value = data);
  }

  // current: coffee beans that meet a minimum requirement of bean weight
  if (tab === 'current') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .gt('total_weight', 100);

    return (beanData.value = data);
  }

  // favorite: coffee beans that are favorited
  if (tab === 'favorites') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .eq('favorite', 'true');

    return (beanData.value = data);
  }
}

onMounted(() => {
  fetchData();
});

watch(() => tab, fetchData);

watchEffect(() => {
  const subscriptionInsert = supabase
    .channel('coffee_beans')
    .on('postgres_changes', { event: 'INSERT', schema: 'public' }, () => {
      fetchData();
    })
    .on('postgres_changes', { event: 'DELETE', schema: 'public' }, () => {
      fetchData();
    })
    .subscribe();

  return () => {
    supabase.removeChannel(subscriptionInsert);
  };
});

const handleUpdateFavorite = (id, value) => {
  // const bean = coffeeBeans.value.find((b) => b.id === id);
  // if (bean) bean.favorite = value;
  beanData.value.map((bean) =>
    bean.id === id ? { ...bean, favorite: value } : bean
  );
};
</script>

<template>
  <div
    v-if="beanData.length > 0"
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
      @update-favorite="handleUpdateFavorite"
    />
  </div>
  <div v-else class="text-2xl">Add coffee beans!</div>
</template>
