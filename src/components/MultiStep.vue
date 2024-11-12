<script setup lang="ts">
import { ref, watch } from "vue";
import FormStep from "./FormStep.vue";
import PreviewCard from "../components/card/Preview.vue";

import type { Form } from "../types/form";

const props = defineProps<{
  stepForm: Form;
}>();

const currentStep = ref<number>(1);
const formData = ref<Record<string, any>>({});
const showPreview = ref<boolean>(false);

const isCurrentStepValid = ref<boolean>(false);

watch(isCurrentStepValid, (newValue) => {
  console.log(newValue);
});

const handleValidation = (isValid: boolean) => {
  isCurrentStepValid.value = isValid;
};

const nextStep = () => {
  if (isCurrentStepValid.value && currentStep.value < props.stepForm.length) {
    currentStep.value++;
  }
};

const prevStep = () => {
  if (currentStep.value > 1) {
    currentStep.value--;
  }
};

const handleSubmit = () => {
  if (isCurrentStepValid.value) {
    console.log("Form Data:", formData.value);
    showPreview.value = true;
  }
};

const handleClosePreview = () => {
  showPreview.value = false;
  formData.value = {};
  currentStep.value = 1;
};
</script>

<template>
  <div class="max-w-5xl px-8 pb-4 mx-auto mt-10">
    <div class="pb-5 text-center">
      <h1 class="text-3xl font-bold text-gray-800">
        Form Data Karyawan
      </h1>
      <p class="mt-2 font-semibold text-gray-600">
        {{ props.stepForm[currentStep - 1].description }}
      </p>

      <p class=""></p>
    </div>

    <!-- Lokasi -->
     <div class="flex items-center justify-center w-full font-medium">
      <div
        class="flex items-center justify-center gap-2 px-4 py-2 text-sm border-2 border-red-900 rounded-full sm:py-2"
      >
        <span
          class="size-[20px] flex items-center justify-center border-2 rounded-full border-red-900"
          >1</span
        >
        <span class="">Kompetisi Keahlian </span>
      </div>
      <div class="border-2"></div>

      <div class="hidden sm:flex w-[40px] bg-gray-200 h-0.5 dark:bg-gray-300" />

      <div
        class="flex items-center justify-center gap-2 px-4 py-2 text-sm text-gray-600 border-2 border-gray-300 rounded-full"
      >
        <span
          class="size-[20px] flex items-center justify-center border-2 rounded-full border-gray-300"
          >2</span
        >
        <span>Tentang Sekolah </span>
      </div>
    </div>

    <!-- dua
   -->
     <!-- <div class="flex items-center justify-center mb-8 gap-x-2">
        <div
          v-for="(item, index) in props.stepForm"
          :key="index"
          class="flex items-center gap-x-2"
        >
          <div
            :class="{
              'flex items-center justify-center gap-2 px-4 py-2 text-sm border-2  rounded-full sm:py-2 font-semibold': true,
              ' text-gray-800 border-red-900': currentStep >= item.step,
              'border-gray-300': currentStep < item.step,
            }"
          >
            <span
              class="size-[20px] flex items-center justify-center border-2 rounded-full border-red-900"
              >1</span
            >
            <span>{{ props.stepForm[currentStep - 1].title }}</span>
          </div>
          <div
            v-if="index !== props.stepForm.length - 1"
            :class="{
              'w-24 md:w-12 h-[2px]': true,
              'bg-gray-300': currentStep >= item.step,
              'bg-gray-400': currentStep < item.step,
            }"
          ></div>
        </div>
      </div> -->
    

    <!-- Bagian -->
    <div class="w-full pb-4 mt-3 border-b-2">
      <p class="text-lg font-medium text-gray-600">Bagian 1</p>
      <h1 class="mt-1 text-xl font-semibold"> {{ props.stepForm[currentStep - 1].title }}</h1>
    </div>
    
   
    <form
      v-if="!showPreview"
      @submit.prevent="handleSubmit"
      class="flex flex-col gap-y-3"
    >
     

      <FormStep
        :key="currentStep"
        :fields="props.stepForm[currentStep - 1].fields"
        :modelValue="formData.value"
        @update:modelValue="(newData) => (formData.value = newData)"
        @validation="handleValidation"
      />

      <div class="flex w-full justify-betweeen"></div>

      <div class="flex justify-end gap-2 mt-4">
        <button
          type="button"
          class="px-3 py-2 font-semibold text-red-900 bg-white border-2 border-red-900 rounded-lg cursor-pointer lg:px-4 fon hover:bg-gray-100 text"
        >
          Previous
        </button>
        <button
          v-if="currentStep !== props.stepForm.length"
          type="button"
          class="px-4 py-2 font-semibold text-white bg-red-900 border-2 rounded-lg cursor-pointer lg:px-5 hover:bg-red-800"
          :class="{
            'opaci0 cursor-not-allowed': !isCurrentStepValid,
          }"
        >
          Next
        </button>
        <button
          v-if="currentStep === stepForm.length"
          type="submit"
          :disabled="!isCurrentStepValid"
          class="px-4 py-2 text-red-900 bg-white border-2 rounded"
          :class="{
            'opacity-50 cursor-not-allowed': !isCurrentStepValid,
          }"
        >
          Submit
        </button>
      </div>
    </form>

    <PreviewCard
      v-if="showPreview"
      :formData="formData"
      @close="handleClosePreview"
    />
  </div>
</template>

<style scoped>
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
