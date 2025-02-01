<script setup>
import {
  GlobeAmericasIcon,
  ArrowsRightLeftIcon,
  SparklesIcon,
  FireIcon,
} from '@heroicons/vue/24/outline';

import CardModal from './card-modal.vue';
import { ref } from 'vue';

const { brand, name } = defineProps(['brand', 'name']);

const showModal = ref(false);
</script>

<template>
  <div
    class="bg-neutral-100 cursor-pointer border border-neutral-200 rounded-lg shadow-md p-6 max-w-96 h-[420px] relative transition-transform duration-300 hover:shadow-lg hover:-translate-y-1"
    :class="{ '-translate-y-1': showModal }"
    @click="showModal = true"
  >
    <div class="flex flex-col space-y-4">
      <div class="space-y-2">
        <h2 class="text-2xl font-bold capitalize">{{ brand }}</h2>
        <p class="text-xl capitalize">{{ name }}</p>
        <p class="text-sm">roast date</p>
      </div>

      <div>
        <div
          class="flex justify-between items-end border-t border-neutral-200 pt-4"
        >
          <h2 class="text-md leading-none">Beans Remaining:</h2>
          <p class="text-xs leading-none">100g / 100g</p>
        </div>

        <div class="w-full h-3 bg-neutral-700 rounded-lg mt-1">
          <!-- dynamic style for bar -->
          <div class="h-3 rounded-lg w-4/5 bg-green-500"></div>
        </div>
      </div>

      <div
        class="grid grid-cols-6 gap-x-8 gap-y-6 border-t border-neutral-200 pt-4"
      >
        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <FireIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Roast</h3>
          </div>
          <p class="text-neutral-600 caapitalize">Medium</p>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <ArrowsRightLeftIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Process</h3>
          </div>
          <p class="text-neutral-600 caapitalize">Washed</p>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <SparklesIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Flavor Notes</h3>
          </div>

          <ul class="text-neutral-600">
            <li>Brown Sugar</li>
            <li>Caramel</li>
            <li>Cocoa</li>
          </ul>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <GlobeAmericasIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Origin</h3>
          </div>
          <p class="text-neutral-600 caapitalize">Kenya</p>
        </div>
      </div>
    </div>
  </div>

  <!-- Probably won't need to use slots to pass the data needed for the modal -->
  <Teleport to="body">
    <CardModal
      :show="showModal"
      @close="showModal = false"
      :brand="brand"
      :name="name"
    >
    </CardModal>
  </Teleport>
</template>
