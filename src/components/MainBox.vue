<script setup lang="ts"></script>

<template>
  <div
    class="from-primary to-secondary text-primary-content grid place-items-center items-end bg-gradient-to-br pt-20 h-screen relative"
  >

    <div
      class="text-base-content glass xl:rounded-box bg-opacity-60 xl:pb-0 absolute MainBox inset-0 m-auto"
    >
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.MainBox {
  width: 1080px;
  height: 720px;
}
</style>
