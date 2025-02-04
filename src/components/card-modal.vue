<script setup>
import { ref } from 'vue';

import { XMarkIcon } from '@heroicons/vue/24/outline';

import { supabase } from '../lib/supabase-client';

const { show, data } = defineProps(['show', 'data']);

const showConfirmDelete = ref(false);

const formatDate = (date) => {
  const year = date.split('-')[0];
  const month = date.split('-')[1];
  const day = date.split('-')[2];

  return `${month}/${day}/${year}`;
};

const deleteBeanData = async (id) => {
  const { error } = await supabase.from('coffee_beans').delete().eq('id', id);
};
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="
        $emit('close');
        showConfirmDelete = false;
      "
      class="fixed inset-0 z-50 top-0 left-0 w-full h-full bg-black/50 flex flex-col gap-6 justify-center items-center transition-opacity duration-300 ease"
    >
      <div
        @click.stop="showConfirmDelete = false"
        class="w-96 p-6 bg-neutral-100 rounded-lg shadow-md transition-all duration-900 ease"
      >
        <div class="space-y-2 flex flex-col items-center relative">
          <button
            class="absolute -top-3 -right-3 cursor-pointer hover:bg-neutral-200 hover:rounded-full h-8 w-8 flex justify-center items-center"
            @click="
              $emit('close');
              showConfirmDelete = false;
            "
          >
            <XMarkIcon class="size-6 text-stone-500" />
          </button>

          <h2 class="text-2xl font-bold capitalize">{{ data.brand }}</h2>
          <p class="text-xl capitalize">{{ data.name }}</p>
          <p class="text-sm">{{ formatDate(data.roast_date) }}</p>
        </div>

        <div
          class="border-t border-neutral-200 mt-4 mb-8 pt-4 flex justify-around"
        >
          <div class="text-center">
            <h3 class="font-semibold text-neutral-700">Grind Setting</h3>
            <p class="text-neutral-600">{{ data.grind }}</p>
          </div>

          <div class="text-center">
            <h3 class="font-semibold text-neutral-700">Brew Method</h3>
            <p class="text-neutral-600 capitalize">{{ data.brew_method }}</p>
          </div>
        </div>

        <div class="mt-6">
          <h3 class="font-semibold text-neutral-700 ml-1 mb-1">
            Additional Notes:
          </h3>
          <div
            class="bg-neutral-200 border border-neutral-300 shadow-sm px-4 py-2 rounded-lg min-h-24 max-h-96 overflow-y-scroll"
          >
            <p class="text-neutral-600 leading-relaxed">
              {{ data.personal_notes }}
            </p>
          </div>
        </div>

        <div className="mt-10 flex justify-between">
          <button
            :disabled="showConfirmDelete"
            @click.stop="showConfirmDelete = true"
            class="px-4 py-2 rounded-lg text-lg bg-red-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
          >
            Remove
          </button>

          <button
            @click.stop
            class="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
          >
            Edit
          </button>
        </div>
      </div>

      <Transition name="modal">
        <div
          v-if="showConfirmDelete"
          @click.stop
          class="w-1/5 bg-neutral-100 p-6 rounded-lg flex flex-col items-center gap-3 transition-opacity duration-1000 ease"
        >
          <h3 class="text-lg text-semibold">
            Are you sure you want to delete?
          </h3>
          <div class="flex gap-3">
            <button
              @click="showConfirmDelete = false"
              class="px-2 py-1 rounded-lg text-md bg-neutral-400 text-neutral-50 font-medium w-20 h-10 text-center shadow-md hover:shadow-none hover:bg-neutral-500 transition duration-200"
            >
              Cancel
            </button>
            <button
              @click="deleteBeanData(data.id)"
              class="px-2 py-1 rounded-lg text-md bg-red-500 text-neutral-50 font-medium w-20 h-10 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
            >
              Confirm
            </button>
          </div>
        </div>
      </Transition>
    </div>
  </Transition>
</template>

<style>
.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from,
.modal-leave-to {
  transition: opacity 0.3s ease-in-out;
}

.confirm-popin-enter-from {
  opacity: 0;
}

.confirm-popin-leave-to {
  opacity: 0;
}

.confirm-popin-enter-from,
.confirm-popin-leave-to {
  transition: opacity 0.5s ease-in-out;
}
</style>
