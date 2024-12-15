<script setup lang="ts">
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectLabel,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';

defineEmits<{
  (e: 'selectedTopic', value: any): void;
}>();
</script>
<template>
  <Select @update:modelValue="$emit('selectedTopic', $event)">
    <SelectTrigger class="w-[180px]">
      <SelectValue placeholder="Select vda topic" />
    </SelectTrigger>
    <SelectContent>
      <SelectGroup>
        <SelectLabel>VDA5050 Topic</SelectLabel>
        <SelectItem value="auto"> Auto </SelectItem>
        <SelectItem value="state"> State </SelectItem>
        <SelectItem value="order"> Order </SelectItem>
        <SelectItem value="connection"> Connection </SelectItem>
        <SelectItem value="instantActions"> Instant Actions </SelectItem>
        <SelectItem value="factsheet"> Factsheet </SelectItem>
        <SelectItem value="visualization"> Visualization </SelectItem>
      </SelectGroup>
    </SelectContent>
  </Select>
</template>
<style></style>
