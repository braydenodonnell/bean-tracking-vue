<script setup>
import { PlusIcon } from '@heroicons/vue/24/outline';

import { ref } from 'vue';
import AddBeanForm from './views/add-bean-form.vue';
const showInputField = ref(false);
</script>

<template>
  <button
    class="absolute top-6 right-6 flex gap-2 capitalize bg-green-500 hover:bg-green-600 rounded-full items-center py-2 px-4 text-neutral-50 text-lg shadow-md transition cursor-pointer"
    @click="showInputField = true"
  >
    <PlusIcon class="size-5" />
    Add new
  </button>

  <Teleport to="body">
    <AddBeanForm
      :show="showInputField"
      @close="showInputField = false"
    ></AddBeanForm>
  </Teleport>
</template>
