<script setup lang="ts">
import { reactive , ref} from "vue";
import { io } from "socket.io-client";
import MainBox from '@/components/MainBox.vue'
import NavHeader from './components/NavHeader.vue';
import InputBox from './components/InputBox.vue';
import ChatItem, {ChatDataItem} from './components/ChatItem.vue';
import JoinModal, {JoinEvent} from './components/JoinModal.vue';
import DrawerBox from "./components/DrawerBox.vue";

const socket = io('ws://localhost:5432');

interface User {
  name: string,
  avatar: string,
  id: string,
  new: boolean
}
const chatData = ref<ChatDataItem[]>([])
const drawerShow = ref(false)
const groupName = ref<string>('聊天室')
const curUser = reactive({
  name:'',
  avatar: '',
  id: ''
})
const chatUserId = ref('')
const userMessage = ref('')
const userChatData = ref<Map<string, ChatDataItem[]>>(new Map())
const userList = ref<Map<string, User>>(new Map())
const message = ref('')

const handleJoin = (e: JoinEvent) => {
  socket.emit('join', Object.assign({},e))
}
const handleOpenDrawer = (user: User) => {
  chatUserId.value = user.id
  const isNew = userList.value.get(chatUserId.value)
  if(isNew) {isNew.new = false}
  drawerShow.value = true
}

socket.on('joined', e=> {
  curUser.avatar = e.avatar
  curUser.id = e.id
  curUser.name = e.name
})

socket.on('welcome', ({name, uList}) => {
  uList.forEach((item: any) => {
    const [id, value] = item
    userList.value.set(id, value)
  });
  chatData.value.push({
    type: 'tips',
    id: Math.random().toString().split('.')[1].slice(0,10),
    content: `欢迎${name}加入群聊~`
  })
})
const handleSend = (val: string) => {
  const obj = {
    id: Math.random().toString().split('.')[1].slice(0, 10),
    name: curUser.name,
    avatar: curUser.avatar,
    userId: curUser.id,
    content: val
  }
  const type:'me' = 'me'
  chatData.value.push(Object.assign({},obj,{type}))
  message.value = ''
  socket.emit('send', obj)
}
socket.on('message', (e: any) => {
  const msg = Object.assign({}, {type: 'your'}, e)
  chatData.value.push(msg)
})
const handleSendUser = (val: string) => {
  const obj = {
    id: Math.random().toString().split('.')[1].slice(0,10),
    name: curUser.name,
    avatar: curUser.avatar,
    content: val,
    userId: curUser.id,
    sendUserId: chatUserId.value
  }
  const type: 'me' = 'me'
  if(!userChatData.value.has(chatUserId.value)) {
    userChatData.value.set(chatUserId.value, [])
  }
  const _chatData = userChatData.value.get(chatUserId.value)
  _chatData?.push(Object.assign({},obj,{type}))
  userMessage.value = ''
  socket.emit('send-user', obj)
}
socket.on('message-user', e=> {
  const msg = Object.assign({}, e, {type: 'your'})
  const sendId = e.userId
  if(!userChatData.value.has(sendId)) {
    userChatData.value.set(sendId, [])
  }
  const chatData = userChatData.value.get(sendId)
  chatData?.push(msg)
  const u = userList.value.get(sendId)
  if (u) {
    u.new = true
  }
})
socket.on('quit', (id: string) => {
  const user = userList.value.get(id)
  userList.value.delete(id)
  chatData.value.push({
    type: 'tips',
    id: Math.random().toString().split('.')[1].slice(0, 10),
    content: user?.name + '退出群聊~',
  })
})
const handleClickUserAvatar = (e: User) => {
  if (e.id === curUser.id) {
    return
  }
  handleOpenDrawer(e)
}
</script>

<template>
  <MainBox>
    <NavHeader
    :group-name="groupName"
    :person-number="userList.size"
    :user-list="userList"
    :cur-user-id="curUser.id"
    @more="handleOpenDrawer"></NavHeader>
    <div class="px-4">
      <ChatItem :chat-data="chatData" @click-user="handleClickUserAvatar"></ChatItem>
    </div>
    <InputBox v-model="message" @send="handleSend"></InputBox>
  </MainBox>

  <JoinModal @join="handleJoin"></JoinModal>
  <DrawerBox v-model="drawerShow">
    <div class="p-4 w-[920px]">
      <div class="px-4">
        <h4 class="text-center mb-2 text-xl">
          {{ userList.get(chatUserId)?.name }}
        </h4>
        <ChatItem
          style="height: calc(100vh - 134px)"
          :chat-data="(userChatData.get(chatUserId) as ChatDataItem[])"
        />
      </div>
      <InputBox v-model="userMessage" @send="handleSendUser" />
    </div>
  </DrawerBox>
</template>

<style scoped></style>