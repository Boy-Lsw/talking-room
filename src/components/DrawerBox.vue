<script setup lang="ts">
import { ref, computed } from 'vue';

interface Props {
  modelValue: boolean
}
const props = defineProps<Props>()
const emit = defineEmits(['update:modelValue'])
const aniShow = ref(true)
const show = computed({
  get() {
    aniShow.value = props.modelValue
    return props.modelValue
  },
  set(val) {
    emitUpdate(val)
  }
})
const emitUpdate = (val: boolean, delay= 300) => {
  aniShow.value = val
  window.setTimeout(() => {
    emit('update:modelValue', val)
  },delay)
}
</script>

<template>
  <div class="h-screen w-screen fixed z-10 top-0" v-show="show">
    <div
      class="drawer-overlay absolute inset-0 z-0"
      @click="emitUpdate(false)"
    ></div>
    <Transition>
      <div
        v-show="aniShow"
        class="drawer-content absolute right-0 top-0 cursor-default h-screen bg-base-100"
      >
        <slot></slot>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.drawer-overlay {
  --tw-bg-opacity: 0.4;
  opacity: 0.999999;
  cursor: pointer;
  background-color: hsl(var(--nf, var(--n)) / var(--tw-bg-opacity));
  transition: all 0.3s;
}
.v-enter-active,
.v-leave-active {
  transition: all 0.3s;
}

.v-enter-from,
.v-leave-to {
  transform: translateX(100%);
}
</style>