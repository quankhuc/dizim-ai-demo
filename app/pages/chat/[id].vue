<script setup lang="ts">
import Button from '~/components/ui/Button.vue'
import Input from '~/components/ui/Input.vue'

const aiImage = 'AI'
const userImage = 'You'

interface Message {
  id: number
  content: string
  sender: 'ai' | 'user'
  timestamp: Date
}

const messages = ref<Message[]>([
  { id: 1, content: 'Hello! How can I help you today?', sender: 'ai', timestamp: new Date() },
  { id: 2, content: 'Hi! I have a question about coding.', sender: 'user', timestamp: new Date() },
  { id: 3, content: 'Sure! I\'d be happy to help. What would you like to know?', sender: 'ai', timestamp: new Date() },
])

const newMessage = ref('')
const chatContainer = ref<HTMLElement | null>(null)

function sendMessage() {
  if (!newMessage.value.trim())
    return

  // Add user message
  messages.value.push({
    id: messages.value.length + 1,
    content: newMessage.value,
    sender: 'user',
    timestamp: new Date(),
  })

  // Simulate AI response
  setTimeout(() => {
    messages.value.push({
      id: messages.value.length + 1,
      content: 'This is a simulated AI response to your message.',
      sender: 'ai',
      timestamp: new Date(),
    })
  }, 1000)

  newMessage.value = ''
}

// Scroll to bottom when new messages arrive
watch(() => messages.value.length, () => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight
    }
  })
})
</script>

<template>
  <div class="mx-auto h-[calc(100vh-4rem)] max-w-3xl flex flex-col p-4">
    <h1 class="mb-4 text-2xl font-bold">
      Chat
    </h1>

    <!-- Chat messages container -->
    <div ref="chatContainer" class="mb-4 flex-1 overflow-y-auto p-4 space-y-4">
      <div
        v-for="message in messages" :key="message.id" class="flex items-start gap-2"
        :class="message.sender === 'ai' ? 'justify-start' : 'justify-end'"
      >
        <div
          v-if="message.sender === 'ai'"
          class="h-8 w-8 flex items-center justify-center rounded-full bg-gray-200 text-sm font-medium"
        >
          {{ aiImage }}
        </div>

        <div
          class="max-w-[70%] rounded-lg p-3" :class="message.sender === 'ai'
            ? 'bg-white text-black border border-gray-200'
            : 'bg-blue-500 text-white'"
        >
          {{ message.content }}
        </div>

        <div
          v-if="message.sender === 'user'"
          class="h-8 w-8 flex items-center justify-center rounded-full bg-blue-200 text-sm font-medium"
        >
          {{ userImage }}
        </div>
      </div>
    </div>

    <!-- Message input -->
    <div class="border-t pt-4">
      <form class="flex gap-2" @submit.prevent="sendMessage">
        <Input v-model="newMessage" type="text" placeholder="Type your message..." />
        <Button type="submit">
          Send
        </Button>
      </form>
    </div>
  </div>
</template>
