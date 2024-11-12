<script setup lang="ts">
import { toRefs, ref, watch } from "vue";
import { type Field } from "../types/form";

const props = defineProps<{
  fields: Field[];
  modelValue: Record<string, any>;
}>();

const emit = defineEmits(["update:modelValue", "validation"]);

const { fields, modelValue } = toRefs(props);
const formData = ref({ ...modelValue.value });

watch(
  formData,
  (newValue) => {
    emit("update:modelValue", newValue);
    emit("validation", validateFields());
  },
  { deep: true }
);

const validateFields = () => {
  return fields.value.every((field) => {
    if (field.required && !formData.value[field.label]) {
      return false;
    }
    return true;
  });
};
</script>

<template>
  <div class="w-full p-0 mt-5 text-gray-600 bg-white">
    <div v-for="field in fields" :key="field.label" class="mb-3">
      <label class="block mb-1 font-medium text-md">
        {{ field.label }}
      </label>
      <div v-if="field.type === 'textfield'">
        <!-- <input
          type="text"
          :placeholder="field.placeholder"
          v-model="formData[field.label]"
          class="w-full p-2 border-2 border-gray-300 rounded-md focus:outline-cyan-600"
        /> -->

        <input
         type="text"
          :placeholder="field.placeholder"
          v-model="formData[field.label]"
          class="shadow-sm bg-white border-[2px] border-gray-300 outline-none  text-sm rounded-md focus:ring-maroon focus:border-maroon block w-full p-2.5 h-12"
      
        />
      </div>
      <div></div>
      <div v-if="field.type === 'radio'" class="flex gap-2">
        <div
          v-for="option in field.options"
          :key="typeof option === 'object' ? option.value : option"
          class="flex items-center"
        >
          <input
            type="radio"
            :value="typeof option === 'object' ? option.value : option"
            v-model="formData[field.label]"
            class="mr-2 border-gray-300 size-[15px]"
          />
          <label>{{
            typeof option === "object" ? option.value : option
          }}</label>
        </div>
      </div>
      <div v-if="field.type === 'textarea'">
        <textarea
          :placeholder="field.placeholder"
          v-model="formData[field.label]"
          class="w-full p-2 border"
        ></textarea>
      </div>
      <div v-if="field.type === 'autocomplete'">
        <input
          type="text"
          list="autocompleteOptions"
          :placeholder="field.placeholder"
          v-model="formData[field.label]"
          class="w-full p-2 border"
        />
        <datalist id="autocompleteOptions">
          <option
            v-for="option in field.options"
            :key="typeof option === 'object' ? option.value : option"
          >
            {{ option }}
          </option>
        </datalist>
      </div>
    </div>
  </div>
</template>

