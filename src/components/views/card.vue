<script setup>
import { onMounted, ref, watch, watchEffect, computed } from 'vue';
import CardInformation from '../card-information.vue';

import { supabase } from '../../lib/supabase-client';

const beanData = ref([]);

const loading = ref(false);

const { tab, searchQuery } = defineProps(['tab', 'searchQuery']);

async function fetchData() {
  if (tab === 'all') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .order('time_created', { ascending: false });

    if (error) {
      console.log('Error fetching: ', error);
    } else {
      loading.value = false;
      return (beanData.value = data);
    }
  }

  if (tab === 'current') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .gt('remaining_weight', 10)
      .order('time_created', { ascending: false });

    if (error) {
      console.log('Error fetching: ', error);
    } else {
      console.log(data);
      return (beanData.value = data);
    }
  }

  if (tab === 'favorites') {
    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .eq('favorite', 'true')
      .order('time_created', { ascending: false });

    if (error) {
      console.log('Error fetching: ', error);
    } else {
      loading.value = false;
      return (beanData.value = data);
    }
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
    .on('postgres_changes', { event: 'UPDATE', schema: 'public' }, () => {
      fetchData();
    })
    .subscribe();

  return () => {
    supabase.removeChannel(subscriptionInsert);
  };
});

const filteredData = computed(() => {
  if (searchQuery) {
    const search = searchQuery.toLowerCase();
    return beanData.value.filter((row) => {
      return Object.keys(row).some((key) => {
        return String(row[key]).toLowerCase().indexOf(search) > -1;
      });
    });
  }

  return beanData.value;
});
</script>

<template>
  <div
    v-if="filteredData.length"
    class="grid grid-cols-1 gap-12 mb-16 md:grid-cols-2"
    :class="{
      'lg:grid-cols-3': filteredData.length >= 3,
      'lg:grid-cols-2': filteredData.length === 2,
      'lg:grid-cols-1': filteredData.length === 1,
    }"
  >
    <CardInformation
      v-for="coffee in filteredData"
      :key="coffee.id"
      :data="coffee"
    />
  </div>
  <div v-else-if="beanData.length > 0" class="text-2xl">No beans found.</div>
  <div v-else class="text-2xl">Add coffee beans!</div>
</template>
