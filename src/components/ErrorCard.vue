<script setup lang="ts">
import {ErrorObject} from 'ajv';

const props = defineProps<{
  validateError: ErrorObject;
}>();
</script>
<template>
  <div class="p-4 max-w mx-auto bg-white">
    <div class="text-xl font-semibold mb-2">Error Details</div>
    <div class="space-y-3">
      <div v-if="props.validateError.keyword" class="text-sm text-gray-600">
        <strong>Keyword:</strong>
        {{ props.validateError.keyword }}
      </div>
      <div
        v-if="props.validateError.instancePath"
        class="text-sm text-gray-600"
      >
        <strong>Instance Path:</strong>
        {{ props.validateError.instancePath }}
      </div>
      <div v-if="props.validateError.schemaPath" class="text-sm text-gray-600">
        <strong>Schema Path:</strong>
        {{ props.validateError.schemaPath }}
      </div>
      <div v-if="props.validateError.params" class="text-sm text-gray-600">
        <strong>Params:</strong>
        {{ props.validateError.params }}
      </div>
      <div
        v-if="props.validateError.propertyName"
        class="text-sm text-gray-600"
      >
        <strong>Property Name:</strong>
        {{ props.validateError.propertyName }}
      </div>
      <div v-if="props.validateError.message" class="text-sm text-gray-600">
        <strong>Message:</strong>
        {{ props.validateError.message }}
      </div>
    </div>
  </div>
</template>
<style></style>
