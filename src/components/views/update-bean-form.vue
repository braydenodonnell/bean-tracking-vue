<script setup>
import { ref, watch } from 'vue';

import { supabase } from '../../lib/supabase-client';

const { show, data } = defineProps(['show', 'data']);

const emit = defineEmits(['close', 'submit']);

const beanData = ref({
  brand: data.brand,
  coffeeName: data.name,
  roastDate: data.roast_date,
  startingWeight: data.total_weight,
  remainingWeight: data.remaining_weight,
  roastLevel: data.roast_level,
  process: data.process,
  flavorNotes: data.flavor_notes,
  origin: data.origin,
  grindSetting: data.grind,
  brewMethod: data.brew_method,
  personalNotes: data.personalNotes,
});

const tzoffset = new Date().getTimezoneOffset() * 60000;
const maxDate = new Date(Date.now() - tzoffset)
  .toISOString()
  .slice(0, -1)
  .split('T')[0];

const roastLevels = ['Light', 'Medium-Light', 'Medium', 'Medium-Dark', 'Dark'];

const processes = ['Washed', 'Natural', 'Honey', 'Wet Hulled', 'Other'];

const brewMethods = [
  'Pour Over',
  'Espresso',
  'French Press',
  'AeroPress',
  'Drip',
  'Cold Brew',
  'Other',
];

const submitted = ref(false);

const errors = ref({});

const validateForm = () => {
  errors.value = {};
  const requiredFields = [
    'brand',
    'coffeeName',
    'roastDate',
    'startingWeight',
    'remainingWeight',
    'flavorNotes',
    'origin',
    'roastLevel',
    'process',
  ];

  requiredFields.forEach((field) => {
    if (!beanData.value[field]) {
      errors.value[field] = 'This field is required';
    }
  });

  if (beanData.value.remainingWeight > beanData.value.startingWeight) {
    errors.value.remainingWeight =
      "Weight can't be greater than starting weight";
  }

  return Object.keys(errors.value).length === 0;
};

const handleUpdate = async (id) => {
  if (validateForm()) {
    submitted.value = true;

    const { error } = await supabase
      .from('coffee_beans')
      .update([
        {
          brand: beanData.value.brand,
          name: beanData.value.coffeeName,
          roast_date: beanData.value.roastDate,
          total_weight: beanData.value.startingWeight,
          remaining_weight: beanData.value.remainingWeight,
          roast_level: beanData.value.roastLevel,
          process: beanData.value.process,
          flavor_notes: beanData.value.flavorNotes,
          origin: beanData.value.origin,
          grind: beanData.value.grindSetting,
          brew_method: beanData.value.brewMethod,
          personal_notes: beanData.value.personalNotes,
        },
      ])
      .eq('id', id);

    if (error) {
      console.error('Error inserting data: ', error);
    }

    // Close form
    emit('close');

    // Display a toast message with confirmation
  }
};

watch(
  () => show,
  (newVal) => {
    if (newVal) {
      beanData.value = {
        brand: data.brand,
        coffeeName: data.name,
        roastDate: data.roast_date,
        startingWeight: data.total_weight,
        remainingWeight: data.remaining_weight,
        roastLevel: data.roast_level,
        process: data.process,
        flavorNotes: data.flavor_notes,
        origin: data.origin,
        grindSetting: data.grind,
        brewMethod: data.brew_method,
        personalNotes: data.personalNotes,
      };

      errors.value = {};

      submitted.value = false;
    }
  }
);
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      class="fixed overflow-hidden bg-black/25 inset-0 z-50 flex justify-end items-center cursor-default transition-opacity duration-300 ease"
    >
      <div
        @click.stop
        class="bg-neutral-100 border border-neutral-200 h-screen rounded-lg shadow-md p-8 w-[40rem] overflow-y-scroll cursor-default overscroll-contain"
      >
        <form @submit.prevent="handleUpdate(data.id)">
          <div class="flex flex-col gap-6">
            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Brand *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': errors.brand }"
                v-model="beanData.brand"
              />
              <p v-if="errors.brand" class="text-red-500 text-sm mt-1">
                {{ errors.brand }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Coffee Name *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': errors.coffeeName }"
                v-model="beanData.coffeeName"
              />
              <p v-if="errors.coffeeName" class="text-red-500 text-sm mt-1">
                {{ errors.coffeeName }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Roast Date *</label
              >
              <input
                type="date"
                :max="maxDate"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': errors.roastDate }"
                v-model="beanData.roastDate"
              />
              <p v-if="errors.roastDate" class="text-red-500 text-sm mt-1">
                {{ errors.roastDate }}
              </p>
            </div>

            <div class="flex gap-6">
              <div>
                <label class="text-md font-medium mb-1 text-neutral-700"
                  >Starting Weight (grams) *</label
                >
                <input
                  type="number"
                  min="0"
                  class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                  :class="{ 'border-red-500': errors.startingWeight }"
                  v-model="beanData.startingWeight"
                />
                <p
                  v-if="errors.startingWeight"
                  class="text-red-500 text-sm mt-1"
                >
                  {{ errors.startingWeight }}
                </p>
              </div>

              <div>
                <label class="text-md font-medium mb-1 text-neutral-700"
                  >Remaining Weight (grams) *</label
                >
                <input
                  type="number"
                  min="0"
                  class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                  :class="{ 'border-red-500': errors.remainingWeight }"
                  v-model="beanData.remainingWeight"
                />
                <p
                  v-if="errors.remainingWeight"
                  class="text-red-500 text-sm mt-1"
                >
                  {{ errors.remainingWeight }}
                </p>
              </div>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Roast Level *</label
              >
              <select
                v-model="beanData.roastLevel"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-11"
                :class="{ 'border-red-500': errors.roastLevel }"
              >
                <option value="" selected disabled hidden>
                  Select roast level
                </option>
                <option
                  v-for="roast in roastLevels"
                  :key="roast"
                  :value="roast"
                >
                  {{ roast }}
                </option>
              </select>
              <p v-if="errors.roastLevel" class="text-red-500 text-sm mt-1">
                {{ errors.roastLevel }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Process *</label
              >
              <select
                v-model="beanData.process"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-11"
                :class="{ 'border-red-500': errors.process }"
              >
                <option value="" selected disabled hidden>
                  Select process
                </option>
                <option
                  v-for="process in processes"
                  :key="process"
                  :value="process"
                >
                  {{ process }}
                </option>
              </select>
              <p v-if="errors.process" class="text-red-500 text-sm mt-1">
                {{ errors.process }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Flavor Notes *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': errors.flavorNotes }"
                placeholder="e.g., chocolate, caramel, fruity"
                v-model="beanData.flavorNotes"
              />
              <p v-if="errors.flavorNotes" class="text-red-500 text-sm mt-1">
                {{ errors.flavorNotes }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Origin *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                :class="{ 'border-red-500': errors.origin }"
                v-model="beanData.origin"
              />
              <p v-if="errors.origin" class="text-red-500 text-sm mt-1">
                {{ errors.origin }}
              </p>
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Grind Setting</label
              >
              <input
                type="number"
                min="0"
                max="100"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.grindSetting"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Brew Method</label
              >
              <select
                v-model="beanData.brewMethod"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-11"
              >
                <option value="" selected disabled hidden>
                  Select brew method
                </option>
                <option v-for="brew in brewMethods" :key="brew" :value="brew">
                  {{ brew }}
                </option>
              </select>
            </div>

            <div class="col-span-2">
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Personal Notes</label
              >
              <textarea
                v-model="beanData.personalNotes"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-30 resize-none"
              ></textarea>
            </div>

            <div class="col-span-2 flex justify-between">
              <button
                @click="$emit('close')"
                className="px-4 py-2 rounded-lg text-lg bg-red-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
              >
                Cancel
              </button>

              <button
                type="submit"
                className="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
              >
                Update
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </Transition>
</template>
