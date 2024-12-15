<script setup lang="ts">
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table';

const props = defineProps<{validateErrors: any[]}>();
defineEmits(['selectedErrorIndex']);
</script>
<template>
  <Table>
    <TableHeader>
      <TableRow>
        <TableHead class="w-[100px]"> Index </TableHead>
        <TableHead>Status</TableHead>
        <TableHead>Method</TableHead>
        <TableHead>Path</TableHead>
      </TableRow>
    </TableHeader>
    <TableBody>
      <TableRow
        @click="$emit('selectedErrorIndex', index)"
        v-for="(error, index) in props.validateErrors"
        :key="index"
      >
        <TableCell class="font-medium">
          {{ index + 1 }}
        </TableCell>
        <TableCell class="font-medium">
          {{ error.message }}
        </TableCell>
        <TableCell>
          {{ error.keyword }}
        </TableCell>
        <TableCell>
          {{ error.schemaPath }}
        </TableCell>
      </TableRow>
    </TableBody>
  </Table>
</template>
<style></style>
