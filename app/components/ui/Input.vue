<script setup lang="ts">
import type { HTMLAttributes } from 'vue'
import { cn } from '@/lib/utils'
import { useVModel } from '@vueuse/core'

const props = defineProps<{
  defaultValue?: string | number
  modelValue?: string | number
  class?: HTMLAttributes['class']
  type?: string
  placeholder?: string
  disabled?: boolean
}>()

const emits = defineEmits<{
  (e: 'update:modelValue', payload: string | number): void
}>()

const modelValue = useVModel(props, 'modelValue', emits, {
  passive: true,
  defaultValue: props.defaultValue,
})
</script>

<template>
  <input
    v-model="modelValue"
    :type="type ?? 'text'"
    :placeholder="placeholder"
    :disabled="disabled"
    :class="cn(
      'flex h-9 w-full rounded-md border bg-white dark:bg-gray-900 px-3 py-2',
      'text-sm transition-all duration-200',
      'border-gray-200 dark:border-gray-800',
      'text-gray-900 dark:text-gray-100',
      'placeholder:text-gray-500 dark:placeholder:text-gray-400',
      'hover:bg-opacity-80 dark:hover:bg-opacity-80',
      'focus:outline-none focus:ring-2 focus:ring-gray-400 dark:focus:ring-gray-600',
      'disabled:cursor-not-allowed disabled:opacity-50',
      'file:border-0 file:bg-transparent file:text-sm file:font-medium',
      props.class,
    )"
  >
</template>
