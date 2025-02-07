<script setup>
import { onMounted, ref, watch } from 'vue';

import { supabase } from '../../lib/supabase-client';

import { HeartIcon as HeartIconSolid } from '@heroicons/vue/24/solid';

const { show } = defineProps(['show']);

const makeCoffeeForm = ref(false);
const inputError = ref(false);
const selectedCoffee = ref();
const gramsUsed = ref();

const beanData = ref();

const fetchData = async () => {
  const { data, error } = await supabase
    .from('coffee_beans')
    .select('brand, name, remaining_weight, id, favorite')
    .gt('remaining_weight', 20)
    .order('remaining_weight', { ascending: true });

  return (beanData.value = data);
};

const makeCoffee = async (id, weight) => {
  inputError.value = false;

  const { data, error } = await supabase
    .from('coffee_beans')
    .select('remaining_weight')
    .eq('id', id)
    .single();

  if (error) {
    console.error('Error fetching bean:', error);
    return;
  }

  if (!gramsUsed.value && gramsUsed !== 0) {
    inputError.value = true;
    return;
  }

  const updatedRemainingWeight = data.remaining_weight - weight;

  await supabase
    .from('coffee_beans')
    .update({ remaining_weight: updatedRemainingWeight })
    .eq('id', id);

  $emit('close');
};

onMounted(() => fetchData());

watch(
  () => show,
  () => {
    makeCoffeeForm.value = false;
    fetchData();
  }
);
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="$emit('close')"
      class="fixed overflow-hidden inset-0 bg-black/50 z-50 flex justify-start items-center cursor-default transition-opacity duration-300 ease"
    >
      <div
        @click.stop="makeCoffeeForm = false"
        class="bg-neutral-100 border border-neutral-200 h-screen shadow-xl z-20 p-8 w-[20rem] overflow-y-scroll cursor-default overscroll-contain"
      >
        <h2 class="text-xl font-semibold mb-6">Select coffee to make</h2>

        <div class="flex flex-col gap-8">
          <div
            v-for="bean in beanData"
            @click.stop="
              makeCoffeeForm = true;
              selectedCoffee = bean;
            "
          >
            <div
              class="bg-neutral-100 cursor-pointer border border-neutral-200 rounded-lg shadow-md p-3 transition-transform duration-300 hover:shadow-lg relative"
            >
              <h3 class="text-lg">{{ bean.brand }}</h3>
              <p>{{ bean.name }}</p>
              <p>{{ bean.remaining_weight }}g remaining</p>
              <HeartIconSolid
                class="size-6 text-red-500 absolute top-2 right-2"
                v-if="bean.favorite"
              />
            </div>
          </div>
        </div>
      </div>

      <Transition name="modal">
        <div
          v-if="makeCoffeeForm"
          @click.stop
          class="bg-neutral-100 border border-neutral-200 h-screen rounded-r-lg shadow-md p-8 w-[20rem] overflow-y-scroll cursor-default transition-opacity duration-500"
        >
          <div>
            <div class="flex flex-col items-center gap-1">
              <h3 class="text-2xl">{{ selectedCoffee.brand }}</h3>
              <p>{{ selectedCoffee.name }}</p>
              <p>{{ selectedCoffee.remaining_weight }}g remaining</p>
            </div>

            <div class="mt-4">
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Grams used</label
              >
              <input
                type="num"
                min="0"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': inputError }"
                v-model="gramsUsed"
              />
              <p v-if="inputError" class="text-red-500 text-sm mt-1">
                This field is required
              </p>
            </div>

            <div class="flex justify-center mt-4">
              <button
                @click="
                  makeCoffee(selectedCoffee.id, gramsUsed);
                  if (!inputError) $emit('close');
                "
                class="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
              >
                Make Coffee
              </button>
            </div>
          </div>
        </div>
      </Transition>
    </div>
  </Transition>
</template>
