<script setup lang="ts">
import { ref, watch } from "vue"
import FormStep from "./FormStep.vue"

import type { Form } from "../types/form"

const props = defineProps<{
  stepForm: Form[]
}>()

const currentStep = ref<number>(1)
const formData = ref<Record<string, any>>({})

const isCurrentStepValid = ref<boolean>(false)

watch(isCurrentStepValid, (newValue) => {
  console.log(newValue)
})

const handleValidation = (isValid: boolean) => {
  isCurrentStepValid.value = isValid
}

const nextStep = () => {
  if (isCurrentStepValid.value && currentStep.value < props.stepForm.length) {
    currentStep.value++
  }
}

const prevStep = () => {
  if (currentStep.value > 1) {
    currentStep.value--
  }
}

const handleSubmit = () => {
  if (isCurrentStepValid.value) {
    console.log("Form Data:", formData.value)
    alert(`Form Data: ${JSON.stringify(formData.value.value)}`)
    formData.value = {}
    currentStep.value = 1
  }
}
</script>

<template>
  <div class="w-[50vw] mx-auto mt-10">
    <form @submit.prevent="handleSubmit" class="flex flex-col gap-y-4">
      <div class="flex items-center gap-x-2 justify-center mb-8">
        <div
          v-for="(item, index) in props.stepForm"
          :key="index"
          class="flex items-center gap-x-2"
        >
          <div
            v-if="index === 0"
            class="w-16 md:w-28 h-[2px] bg-blue-700"
          ></div>
          <div
            class="rounded-full w-16 h-16 text-xl font-bold text-center pt-4 cursor-pointer"
            :class="`${
              currentStep === item.step
                ? 'bg-blue-600 text-white'
                : 'bg-gray-200'
            } `"
          >
            <span>{{ item.step }}</span>
          </div>
          <div
            v-if="index !== props.stepForm.length - 1"
            class="w-24 md:w-36 h-[2px] bg-blue-700"
          ></div>
          <div
            v-if="index === props.stepForm.length - 1"
            class="w-16 md:w-28 h-[2px] bg-blue-700"
          ></div>
        </div>
      </div>

      <FormStep
        :key="currentStep"
        :fields="props.stepForm[currentStep - 1].fields"
        :modelValue="formData.value"
        @update:modelValue="(newData) => (formData.value = newData)"
        @validation="handleValidation"
      />

      <div class="flex justify-between mt-8">
        <button
          type="button"
          @click="prevStep"
          :disabled="currentStep === 1"
          class="bg-gray-500 text-white px-4 py-2 rounded"
        >
          Previous
        </button>
        <button
          v-if="currentStep !== props.stepForm.length"
          type="button"
          @click="nextStep"
          :disabled="!isCurrentStepValid"
          class="bg-blue-600 text-white px-4 py-2 rounded"
          :class="{
            'opacity-50 cursor-not-allowed': !isCurrentStepValid,
          }"
        >
          Next
        </button>
        <button
          v-if="currentStep === stepForm.length"
          type="submit"
          :disabled="!isCurrentStepValid"
          class="bg-blue-600 text-white px-4 py-2 rounded"
          :class="{
            'opacity-50 cursor-not-allowed': !isCurrentStepValid,
          }"
        >
          Submit
        </button>
      </div>
    </form>
  </div>
</template>

<style scoped>
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
