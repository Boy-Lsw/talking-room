<script setup lang="ts">
import { computed } from 'vue';

interface Props {
  modelValue: string
}
const props = defineProps<Props>()
const emits = defineEmits(['update:modelValue', 'send'])
const val = computed({
  get() {
    return props.modelValue
  },
  set(newVal) {
    emits('update:modelValue', newVal)
  }
})
const handleSend = () => {
  emits('send', val.value)
}
</script>

<template>
  <div class="form-control mt-4">
    <div class="input-group px-4">
      <input
        type="text"
        placeholder="..."
        class="input input-bordered w-full"
        style="--tw-bg-opacity: 0.7"
        v-model="val"
        @keyup.enter="handleSend"
      />
      <button
        class="btn btn-square"
        style="--tw-bg-opacity: 0.7"
        @click="handleSend"
      >
        发送
      </button>
    </div>
  </div>
</template>

<style scoped></style>