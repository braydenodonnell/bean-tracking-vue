<script setup>
import { XMarkIcon } from '@heroicons/vue/24/outline';

const props = defineProps({
  show: Boolean,
  brand: String,
  name: String,
});
</script>

<template>
  <Transition name="modal">
    <div
      v-if="show"
      @click="$emit('close')"
      class="fixed inset-0 z-50 top-0 left-0 w-full h-full bg-black/50 flex transition-opacity duration-300 ease"
    >
      <div
        @click.stop
        class="w-1/4 m-auto p-6 bg-neutral-100 rounded-lg shadow-md transition-all duration-300 ease relative"
      >
        <div class="space-y-2 flex flex-col items-center relative">
          <button
            class="absolute -top-3 -right-3 cursor-pointer hover:bg-neutral-200 hover:rounded-full h-8 w-8 flex justify-center items-center"
            @click="$emit('close')"
          >
            <XMarkIcon class="size-6 text-stone-500" />
          </button>

          <h2 class="text-2xl font-bold capitalize">{{ brand }}</h2>
          <p class="text-xl capitalize">{{ name }}</p>
          <p class="text-sm">Date</p>
        </div>

        <div
          class="border-t border-neutral-200 mt-4 mb-8 pt-4 flex justify-around"
        >
          <div class="text-center">
            <h3 class="font-semibold text-neutral-700">Grind Setting</h3>
            <p class="text-neutral-600">15</p>
          </div>

          <div class="text-center">
            <h3 class="font-semibold text-neutral-700">Brew Method</h3>
            <p class="text-neutral-600 capitalize">espresso</p>
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
              Lorem ipsum dolor sit, amet consectetur adipisicing elit. Ratione,
              iure. Lorem ipsum dolor sit, amet consectetur adipisicing elit.
              Ratione, iure.
            </p>
          </div>
        </div>

        <div className="mt-10 flex justify-between">
          <button
            className="px-4 py-2 rounded-lg text-lg bg-red-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-red-600 transition duration-200"
          >
            Remove
          </button>

          <button
            className="px-4 py-2 rounded-lg text-lg bg-green-500 text-neutral-50 font-medium w-28 h-12 text-center shadow-md cursor-pointer hover:shadow-none hover:bg-green-600 transition duration-200"
          >
            Edit
          </button>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style>
/* .modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  transition: opacity 0.3s ease;
} */

/* .modal-container {
  width: 300px;
  margin: auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
} */

/* 
.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}
*/

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
