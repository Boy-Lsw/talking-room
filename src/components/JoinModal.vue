<script setup lang="ts">
import { ref } from 'vue';
import avatarList from '@/assets/avatar';
export interface JoinEvent{
  name: string,
  avatar: string
} 

const aList = [...avatarList]
const isOpen = ref(true)
const name = ref('')

const emits = defineEmits({
  join: (e: JoinEvent) => {
    const {name ,avatar} = e
    if(name && avatar) {
      return true
    } else {
      console.warn('未输入名字')
      return false
    }
  }
})

const handleJoin = () => {
  let randomNum = Math.floor(Math.random()*aList.length)
  let avatar = aList[randomNum]
  emits('join', {name: name.value, avatar})
  isOpen.value = false
}
</script>

<template>
  <div :class="['modal', isOpen?'modal-open':'']">
    <div class="modal-box">
      <h3 class="font-bold text-lg text-center">加入群聊</h3>

      <div class="form-control w-full">
        <label class="label">
          <span class="label-text">请输入你的名字</span>
        </label>
        <input
          type="text"
          placeholder="your name"
          class="input input-bordered w-full"
          v-model="name"
          @keyup.enter="handleJoin"
        />
      </div>
      <div class="modal-action justify-center">
        <label for="my-modal" class="btn px-8" @click="handleJoin">进入</label>
      </div>
    </div>
  </div>
</template>

<style scoped></style>