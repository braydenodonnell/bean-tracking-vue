<script setup>
import MakeCoffeeForm from './views/make-coffee-form.vue';

import { PlusIcon } from '@heroicons/vue/24/outline';

import { ref } from 'vue';

const showMakeCoffee = ref(false);
</script>

<template>
  <button
    class="absolute top-6 left-6 flex gap-2 capitalize bg-yellow-800 hover:bg-yellow-900 rounded-full items-center py-2 px-4 text-neutral-50 text-lg shadow-md transition cursor-pointer"
    @click="showMakeCoffee = true"
  >
    <PlusIcon class="size-5" />
    Make Coffee
  </button>

  <Teleport to="body">
    <MakeCoffeeForm
      :show="showMakeCoffee"
      @close="showMakeCoffee = false"
    ></MakeCoffeeForm>
  </Teleport>
</template>
