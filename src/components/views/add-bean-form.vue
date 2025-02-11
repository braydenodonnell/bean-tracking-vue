<script setup>
import { ref, watch } from 'vue';

import { supabase } from '../../lib/supabase-client';

const props = defineProps({
  show: Boolean,
});

const emit = defineEmits(['close', 'submit']);

const beanData = ref({
  brand: '',
  coffeeName: '',
  roastDate: '',
  startingWeight: '',
  roastLevel: '',
  process: '',
  flavorNotes: '',
  origin: '',
  grindSetting: '',
  brewMethod: '',
  personalNotes: '',
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
const showUserChoice = ref(false);
const userChoice = ref(undefined);

const errors = ref({});

const validateForm = () => {
  errors.value = {};
  const requiredFields = [
    'brand',
    'coffeeName',
    'roastDate',
    'startingWeight',
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

  return Object.keys(errors.value).length === 0;
};

const handleSubmit = async () => {
  userChoice.value = undefined;

  if (validateForm()) {
    submitted.value = true;

    // Check if a row with the same brand and name exists
    const { data, error: fetchError } = await supabase
      .from('coffee_beans')
      .select('*')
      .eq('brand', beanData.value.brand.toLowerCase())
      .eq('name', beanData.value.coffeeName.toLowerCase());

    if (fetchError) {
      console.error('Error fetching data:', fetchError);
      return;
    }

    // Brand and name already exist
    if (data.length > 0) {
      showUserChoice.value = true;

      const userConfirmation = await waitForUserChoice();

      if (userConfirmation && userChoice.value) {
        console.log(userConfirmation);
        console.log(userChoice.value);

        // Overwrite
        const { error: updateError } = await supabase
          .from('coffee_beans')
          .update({
            roast_date: beanData.value.roastDate,
            total_weight: beanData.value.startingWeight,
            remaining_weight: beanData.value.startingWeight,
            roast_level: beanData.value.roastLevel.toLowerCase(),
            process: beanData.value.process.toLowerCase(),
            flavor_notes: beanData.value.flavorNotes.toLowerCase(),
            origin: beanData.value.origin.toLowerCase(),
            grind: beanData.value.grindSetting,
            brew_method: beanData.value.brewMethod.toLowerCase(),
            personal_notes: beanData.value.personalNotes,
          })
          .eq('brand', beanData.value.brand.toLowerCase())
          .eq('name', beanData.value.coffeeName.toLowerCase());

        if (updateError) {
          console.error('Error updating data:', updateError);
        } else {
          emit('close');
        }
      } else {
        // New entry
        insertNewEntry();
      }
    } else {
      insertNewEntry();
    }
  }
};

// Function to wait for the user's choice in the modal
const waitForUserChoice = () => {
  return new Promise((resolve) => {
    const interval = setInterval(() => {
      if (userChoice.value !== undefined) {
        clearInterval(interval);
        resolve(userChoice);
      }
    }, 100);
  });
};

const insertNewEntry = async () => {
  const { error } = await supabase.from('coffee_beans').insert({
    brand: beanData.value.brand.toLowerCase(),
    name: beanData.value.coffeeName.toLowerCase(),
    roast_date: beanData.value.roastDate,
    total_weight: beanData.value.startingWeight,
    remaining_weight: beanData.value.startingWeight,
    roast_level: beanData.value.roastLevel.toLowerCase(),
    process: beanData.value.process.toLowerCase(),
    flavor_notes: beanData.value.flavorNotes.toLowerCase(),
    origin: beanData.value.origin.toLowerCase(),
    grind: beanData.value.grindSetting,
    brew_method: beanData.value.brewMethod.toLowerCase(),
    personal_notes: beanData.value.personalNotes,
  });

  if (error) {
    console.error('Error inserting data: ', error);
  } else {
    emit('close');
    // Show a success message
  }
};

watch(
  () => props.show,
  (newVal) => {
    if (newVal) {
      beanData.value = {
        brand: '',
        coffeeName: '',
        roastDate: '',
        startingWeight: '',
        roastLevel: '',
        process: '',
        flavorNotes: '',
        origin: '',
        grindSetting: '',
        brewMethod: '',
        personalNotes: '',
      };

      errors.value = {};

      submitted.value = false;

      showUserChoice.value = false;
    }
  }
);
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="$emit('close')"
      class="fixed overflow-hidden inset-0 bg-black/50 z-50 flex flex-col justify-end items-end cursor-default transition-opacity duration-300 ease"
    >
      <div
        @click.stop
        class="bg-neutral-100 border border-neutral-200 rounded-lg shadow-md p-8 w-[30rem] overflow-y-scroll cursor-default overscroll-contain"
      >
        <form @submit.prevent="handleSubmit">
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
              <p v-if="errors.startingWeight" class="text-red-500 text-sm mt-1">
                {{ errors.startingWeight }}
              </p>
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
                class="px-4 py-2 rounded-lg text-lg bg-red-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
              >
                Cancel
              </button>

              <button
                type="submit"
                class="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
              >
                Add Coffee
              </button>
            </div>
          </div>
        </form>
      </div>

      <Transition name="modal">
        <div
          v-if="showUserChoice"
          class="fixed inset-0 z-50 flex justify-center mt-12 h-fit cursor-default transition-opacity duration-900 ease"
        >
          <div class="flex flex-col gap-6 bg-neutral-100 p-6 w-96 rounded-lg">
            <div class="text-lg">
              A coffee bean with the same brand and name already exists. Do you
              want to overwrite it or make a new entry
            </div>

            <div class="col-span-2 flex justify-between">
              <button
                @click.stop="
                  userChoice = true;
                  showUserChoice = false;
                "
                class="px-4 py-2 rounded-lg text-lg bg-red-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
              >
                Overwrite
              </button>

              <button
                @click.stop="
                  userChoice = false;
                  showUserChoice = false;
                "
                class="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
              >
                New Entry
              </button>
            </div>
          </div>
        </div>
      </Transition>
    </div>
  </Transition>
</template>
