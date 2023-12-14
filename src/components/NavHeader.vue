<script setup lang="ts">
import { computed } from 'vue';

interface Props {
  groupName: string,
  personNumber: number,
  userList: Map<any, any>
  curUserId: string
}
interface User {
  id: string
  avatar: string
  name: string
  new: boolean
}

const props = defineProps<Props>()
const emits = defineEmits(['more'])
const users = computed<User[]>(() => {
  const list: User[] = []
  if(props.userList.size === 0) return []
  props.userList.forEach((val, key) => {
    const {avatar, name} = val
    if(key !== props.curUserId) {
      list.push({
        avatar, 
        name,
        id: key,
        new: val.new
      })
    }
  })
  return list
})

const handleMore = (user: User) => {
  emits('more', user)
}
</script>

<template>
  <div class="navbar text-primary-content rounded-box space-x-1 h-16">
    <div class="flex-1">
      <a class="normal-case text-xl pl-4"
        >{{props.groupName}}({{props.personNumber}}人)</a
      >
    </div>
    <div class="flex-none avatar-list pr-4 bg-red">
      <div
        class="avatar ml-1 cursor-pointer"
        :class="item.new ? 'online' : ''"
        @click="handleMore(item)"
        v-for="item in users"
        :key="item.id"
      >
        <div class="w-6 rounded-full">
          <img :src="item.avatar" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>