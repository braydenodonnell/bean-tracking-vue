<script setup>
import {
  GlobeAmericasIcon,
  ArrowsRightLeftIcon,
  SparklesIcon,
  FireIcon,
  HeartIcon as HeartIconOutline,
} from '@heroicons/vue/24/outline';

import { HeartIcon as HeartIconSolid } from '@heroicons/vue/24/solid';

import { supabase } from '../lib/supabase-client';

import CardModal from './card-modal.vue';

import { ref, watch } from 'vue';

const showModal = ref(false);
const showFavorite = ref(data.favorite);

const { data } = defineProps(['data']);

const formatDate = (date) => {
  const year = date.split('-')[0];
  const month = date.split('-')[1];
  const day = date.split('-')[2];

  return `${month}/${day}/${year}`;
};

const updateFavorite = async (value, id) => {
  const { error } = await supabase
    .from('coffee_beans')
    .update({ favorite: value })
    .eq('id', id);

  if (error) {
    console.error('Error updating favorite: ', error);
  }
};

let percentRemaining =
  (Number(data.remaining_weight) / Number(data.total_weight)) * 100;

watch(
  () => data.remaining_weight,
  (newWeight) => {
    percentRemaining = (Number(newWeight) / Number(data.total_weight)) * 100;
  }
);
</script>

<template>
  <div
    class="bg-neutral-100 fixed cursor-pointer border border-neutral-200 rounded-lg shadow-md p-6 w-80 h-[420px] relative transition-transform duration-300 hover:shadow-lg"
    @click="showModal = true"
  >
    <div class="flex flex-col space-y-4">
      <div class="space-y-2">
        <h2 class="text-2xl font-semibold capitalize">{{ data.brand }}</h2>
        <p class="text-xl capitalize">{{ data.name }}</p>
        <p class="text-sm">{{ formatDate(data.roast_date) }}</p>
      </div>

      <div>
        <div
          class="flex justify-between items-end border-t border-neutral-200 pt-4"
        >
          <h2 class="text-md leading-none">Beans Remaining:</h2>
          <p class="text-xs leading-none">
            {{ data.remaining_weight }}g / {{ data.total_weight }}g
          </p>
        </div>

        <div class="w-full h-3 bg-neutral-700 rounded-lg mt-1 overflow-hidden">
          <div
            class="h-3 rounded-l-lg transition-all duration-300"
            :class="{
              'rounded-lg': percentRemaining === 100,
              'bg-green-500': percentRemaining >= 50,
              'bg-yellow-300': percentRemaining >= 20 && percentRemaining < 50,
              'bg-red-500': percentRemaining < 20,
              'duration-500': data.total_weight < 300,
            }"
            :style="{ width: percentRemaining + '%' }"
          ></div>
        </div>
      </div>

      <div
        class="grid grid-cols-6 gap-x-3 gap-y-6 border-t border-neutral-200 pt-4"
      >
        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <FireIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Roast</h3>
          </div>
          <p class="text-neutral-600 caapitalize">{{ data.roast_level }}</p>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <ArrowsRightLeftIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Process</h3>
          </div>
          <p class="text-neutral-600 caapitalize">{{ data.process }}</p>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <SparklesIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Notes</h3>
          </div>

          <ul class="text-neutral-600">
            <li v-for="note in data.flavor_notes.split(',')" class="capitalize">
              {{ note }}
            </li>
          </ul>
        </div>

        <div class="space-y-1 col-span-3">
          <div class="flex items-center gap-1">
            <GlobeAmericasIcon class="size-5 text-neutral-700" />
            <h3 class="font-semibold text-neutral-700">Origin</h3>
          </div>
          <ul class="text-neutral-600">
            <li v-for="origin in data.origin.split(',')" class="capitalize">
              {{ origin }}
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div
      @click.stop="
        showFavorite = !showFavorite;
        updateFavorite(showFavorite, data.id);
      "
    >
      <HeartIconSolid
        class="size-6 text-red-500 absolute top-4 right-4"
        v-if="showFavorite"
      />
      <HeartIconOutline
        class="size-6 text-red-500 absolute top-4 right-4"
        v-else
      />
    </div>
  </div>

  <Teleport to="body">
    <CardModal
      :show="showModal"
      @close="showModal = false"
      :data="data"
    ></CardModal>
  </Teleport>
</template>
