<script setup>
import { ref } from 'vue';

const props = defineProps({
  show: Boolean,
});

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

const handleSubmit = () => {};
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="$emit('close')"
      class="fixed inset-0 bg-black/50 z-50 flex justify-end items-center cursor-default transition-opacity duration-300 ease"
    >
      <div
        @click.stop
        class="bg-neutral-100 border border-neutral-200 h-screen rounded-lg shadow-md p-8 w-[40rem] overflow-y-auto cursor-default overscroll-contain"
      >
        <form @submit.prevent="console.log(beanData)">
          <div class="grid grid-cols-2 gap-x-12 gap-y-8">
            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Brand *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.brand"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Coffee Name *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.coffeeName"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Roast Date *</label
              >
              <input
                type="date"
                :max="maxDate"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.roastDate"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Starting Weight (grams) *</label
              >
              <input
                type="number"
                min="0"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.startingWeight"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Roast Level *</label
              >
              <select
                v-model="beanData.roastLevel"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-11"
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
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Process *</label
              >
              <select
                v-model="beanData.process"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600 h-11"
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
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Flavor Notes *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                placeholder="e.g., chocolate, caramel, fruity"
                v-model="beanData.flavorNotes"
              />
            </div>

            <div>
              <label class="text-md font-medium mb-1 text-neutral-700"
                >Origin *</label
              >
              <input
                type="text"
                class="w-full px-3 py-2 border-2 border-neutral-300 bg-neutral-200 rounded-lg text-neutral-600"
                v-model="beanData.origin"
              />
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
                Add Coffee
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </Transition>
</template>

<!-- 
<script setup>
import { ref, reactive } from 'vue';

const props = defineProps({
  show: {
    type: Boolean,
    required: true,
  },
});

defineEmits(['close']);

const formData = reactive({
  brand: '',
  coffeeName: '',
  roastDate: '',
  startingWeight: '',
  flavorNotes: '',
  origin: '',
  roastLevel: '',
  process: '',
  grindSetting: '',
  brewMethod: '',
  personalNotes: '',
});

const errors = reactive({});
const submitted = ref(false);

const roastLevels = ['Light', 'Medium-Light', 'Medium', 'Medium-Dark', 'Dark'];
const processes = ['Washed', 'Natural', 'Honey', 'Anaerobic', 'Other'];
const brewMethods = [
  'Pour Over',
  'Espresso',
  'French Press',
  'AeroPress',
  'Drip',
  'Cold Brew',
  'Other',
];

const validateForm = () => {
  const newErrors = {};
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
    if (!formData[field]) {
      newErrors[field] = 'This field is required';
    }
  });

  if (formData.roastDate && !isValidDate(formData.roastDate)) {
    newErrors.roastDate = 'Please enter a valid date';
  }

  if (formData.startingWeight && isNaN(formData.startingWeight)) {
    newErrors.startingWeight = 'Please enter a valid number';
  }

  Object.assign(errors, newErrors);
  return Object.keys(newErrors).length === 0;
};

const isValidDate = (dateString) => {
  const date = new Date(dateString);
  return date instanceof Date && !isNaN(date);
};

const handleSubmit = () => {
  if (validateForm()) {
    submitted.value = true;
    console.log('Form submitted:', formData);

    // You can emit an event here with the form data
    // $emit('submit', formData)
  }
};
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="$emit('close')"
      class="fixed inset-0 bg-black/50 z-50 flex justify-end items-center cursor-default transition-opacity duration-300 ease"
    >
      <div
        @click.stop
        class="bg-white border border-stone-200 rounded-lg shadow-md p-8 w-[40rem] h-full overflow-y-auto cursor-default overscroll-contain"
      >
        <h2 class="text-2xl font-semibold mb-6">Coffee Bean Entry</h2>

        <form @submit.prevent="handleSubmit" class="space-y-6">
          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium mb-1">Brand *</label>
              <input
                v-model="formData.brand"
                type="text"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.brand }"
              />
              <p v-if="errors.brand" class="text-red-500 text-sm mt-1">
                {{ errors.brand }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Coffee Name *</label
              >
              <input
                v-model="formData.coffeeName"
                type="text"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.coffeeName }"
              />
              <p v-if="errors.coffeeName" class="text-red-500 text-sm mt-1">
                {{ errors.coffeeName }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1">Roast Date *</label>
              <input
                v-model="formData.roastDate"
                type="date"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.roastDate }"
              />
              <p v-if="errors.roastDate" class="text-red-500 text-sm mt-1">
                {{ errors.roastDate }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Starting Weight (g) *</label
              >
              <input
                v-model="formData.startingWeight"
                type="number"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.startingWeight }"
              />
              <p v-if="errors.startingWeight" class="text-red-500 text-sm mt-1">
                {{ errors.startingWeight }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Flavor Notes *</label
              >
              <input
                v-model="formData.flavorNotes"
                type="text"
                placeholder="e.g., Chocolate, Berry, Nutty"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.flavorNotes }"
              />
              <p v-if="errors.flavorNotes" class="text-red-500 text-sm mt-1">
                {{ errors.flavorNotes }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1">Origin *</label>
              <input
                v-model="formData.origin"
                type="text"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.origin }"
              />
              <p v-if="errors.origin" class="text-red-500 text-sm mt-1">
                {{ errors.origin }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Roast Level *</label
              >
              <select
                v-model="formData.roastLevel"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.roastLevel }"
              >
                <option value="">Select roast level</option>
                <option
                  v-for="level in roastLevels"
                  :key="level"
                  :value="level.toLowerCase()"
                >
                  {{ level }}
                </option>
              </select>
              <p v-if="errors.roastLevel" class="text-red-500 text-sm mt-1">
                {{ errors.roastLevel }}
              </p>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1">Process *</label>
              <select
                v-model="formData.process"
                class="w-full px-3 py-2 border rounded-md"
                :class="{ 'border-red-500': errors.process }"
              >
                <option value="">Select process</option>
                <option
                  v-for="process in processes"
                  :key="process"
                  :value="process.toLowerCase()"
                >
                  {{ process }}
                </option>
              </select>
              <p v-if="errors.process" class="text-red-500 text-sm mt-1">
                {{ errors.process }}
              </p>
            </div>
          </div>

          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium mb-1"
                >Grind Setting (Optional)</label
              >
              <input
                v-model="formData.grindSetting"
                type="text"
                placeholder="e.g., Fine, Medium, Coarse"
                class="w-full px-3 py-2 border rounded-md"
              />
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Brew Method (Optional)</label
              >
              <select
                v-model="formData.brewMethod"
                class="w-full px-3 py-2 border rounded-md"
              >
                <option value="">Select brew method</option>
                <option
                  v-for="method in brewMethods"
                  :key="method"
                  :value="method.toLowerCase()"
                >
                  {{ method }}
                </option>
              </select>
            </div>

            <div>
              <label class="block text-sm font-medium mb-1"
                >Personal Notes (Optional)</label
              >
              <textarea
                v-model="formData.personalNotes"
                rows="4"
                placeholder="Add your personal notes, preferences, or brewing tips..."
                class="w-full px-3 py-2 border rounded-md"
              ></textarea>
            </div>
          </div>

          <div
            v-if="submitted"
            class="bg-green-50 border border-green-200 rounded-md p-4"
          >
            <p class="text-green-700">
              Coffee bean information successfully recorded!
            </p>
          </div>

          <div class="flex justify-end space-x-4">
            <button
              type="button"
              @click="$emit('close')"
              class="px-4 py-2 border rounded-md hover:bg-gray-50"
            >
              Cancel
            </button>
            <button
              type="submit"
              class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700"
            >
              Save Coffee Bean Entry
            </button>
          </div>
        </form>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}
</style> -->
