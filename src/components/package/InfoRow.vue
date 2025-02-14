<script setup lang="ts">
import debounce from 'lodash.debounce'
import { reactive, ref, watch } from 'vue'
import { toast } from 'vue-sonner'
import { useI18n } from 'vue-i18n'
import copy from 'copy-text-to-clipboard'
import { useDisplayStore } from '~/stores/display'

const props = defineProps<{
  label: string
  value: string
  editable?: boolean
  isLink?: boolean
}>()

const emit = defineEmits<{
  (event: 'update:value', value: string): void
  (event: 'delete', key: string): void
}>()

const { t } = useI18n()
const displayStore = useDisplayStore()

const computedValue = reactive({ value: props.value })
const rowInput = ref(props.value)
watch(rowInput, debounce(() => {
  emit('update:value', rowInput.value)
}, 500))

async function copyKey() {
  copy(computedValue.value)
  console.log('displayStore.messageToast', displayStore.messageToast)
  toast.success(t('key-copied'))
}
</script>

<template>
  <div class="px-4 py-4 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6 sm:py-5">
    <dt class="text-sm font-medium text-gray-700 dark:text-gray-200">
      {{ props.label }}
    </dt>
    <dd
      class="mt-1 text-sm sm:col-span-2 sm:mt-0"
      :class="{
        'cursor-pointer underline underline-offset-4 text-blue-600 active dark:text-blue-500 font-bold text-dust': props.isLink,
        'text-gray-600 dark:text-gray-200': !props.isLink,
      }"
    >
      <div class="flex flex-row">
        <input v-if="editable" v-model="rowInput" class="block w-full p-1 text-gray-900 bg-white border border-gray-300 rounded-lg dark:bg-gray-50 md:w-1/2 dark:border-gray-600 focus:border-blue-500 dark:bg-gray-700 sm:text-xs dark:text-white focus:ring-blue-500 dark:focus:border-blue-500 dark:focus:ring-blue-500 dark:placeholder-gray-400">
        <span v-else @click="copyKey()"> {{ computedValue.value }} </span>
        <slot />
      </div>
    </dd>
  </div>
</template>
