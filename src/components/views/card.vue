<script setup>
import { onMounted, ref, watch, watchEffect } from 'vue';
import CardInformation from '../card-information.vue';

import { supabase } from '../../lib/supabase-client';

const beanData = ref([]);

const loading = ref(false);

const { tab } = defineProps(['tab']);

async function fetchData() {
  // all: all the coffee bean entries in supabase
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

  // current: coffee beans that meet a minimum requirement of bean weight
  if (tab === 'current') {
    loading.true;

    const { data, error } = await supabase
      .from('coffee_beans')
      .select()
      .gt('remaining_weight', 0)
      .order('time_created', { ascending: false });

    if (error) {
      console.log('Error fetching: ', error);
    } else {
      loading.value = false;
      return (beanData.value = data);
    }
  }

  // favorite: coffee beans that are favorited
  if (tab === 'favorites') {
    loading.value = true;

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
</script>

<template>
  <div role="status" v-if="loading">
    <svg
      aria-hidden="true"
      class="w-8 h-8 text-gray-200 animate-spin dark:text-gray-600 fill-blue-400"
      viewBox="0 0 100 101"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
        fill="currentColor"
      />
      <path
        d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
        fill="currentFill"
      />
    </svg>
    <span class="sr-only">Loading...</span>
  </div>
  <div
    v-else-if="beanData.length > 0"
    class="grid grid-cols-1 gap-12 mb-16"
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
  <div v-else class="text-2xl">Add coffee beans!</div>
</template>
