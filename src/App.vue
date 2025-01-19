<script setup lang="ts">
import Validator from './views/Validator.vue';
import Toaster from '@/components/ui/toast/Toaster.vue';
import { Analytics } from "@vercel/analytics/vue";
</script>

<template>
  <Analytics />
  <Toaster />
  <Validator></Validator>
</template>

<style scoped></style>
