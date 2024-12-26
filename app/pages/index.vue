<script setup lang="ts">
import Input from '@/components/ui/Input.vue'

const title = ref('')
const imageSize = ref('')
const selectedFile = ref<File | null>(null)
const previewUrl = ref('')

// Video states
const selectedVideo = ref<File | null>(null)
const videoPreviewUrl = ref('')
const videoDuration = ref('')

function handleFileChange(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files && input.files[0]) {
    selectedFile.value = input.files[0]
    previewUrl.value = URL.createObjectURL(input.files[0])

    // Get image dimensions when loaded
    const img = new Image()
    img.onload = () => {
      imageSize.value = `${img.width}x${img.height}px`
    }
    img.src = previewUrl.value
  }
}

function handleVideoChange(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files && input.files[0]) {
    selectedVideo.value = input.files[0]
    videoPreviewUrl.value = URL.createObjectURL(input.files[0])

    // Get video duration
    const video = document.createElement('video')
    video.onloadedmetadata = () => {
      videoDuration.value = `${Math.round(video.duration)}s`
    }
    video.src = videoPreviewUrl.value
  }
}

function removeImage() {
  selectedFile.value = null
  if (previewUrl.value) {
    URL.revokeObjectURL(previewUrl.value)
    previewUrl.value = ''
  }
  imageSize.value = ''
}

function removeVideo() {
  selectedVideo.value = null
  if (videoPreviewUrl.value) {
    URL.revokeObjectURL(videoPreviewUrl.value)
    videoPreviewUrl.value = ''
  }
  videoDuration.value = ''
}

// Cleanup object URLs on component unmount
onBeforeUnmount(() => {
  if (previewUrl.value)
    URL.revokeObjectURL(previewUrl.value)
  if (videoPreviewUrl.value)
    URL.revokeObjectURL(videoPreviewUrl.value)
})

function handleSave() {
  // TODO: Implement save functionality
  console.log('Saving changes...')
  const id = crypto.randomUUID()
  navigateTo(`/chat/${id}`)
}

function handleDelete() {
  // TODO: Implement delete functionality
  console.log('Deleting assistant...')
}
</script>

<template>
  <div class="mx-auto max-w-4xl p-4 space-y-6">
    <div>
      <div class="mb-2 text-2xl font-bold">
        Tên cho trợ lý ảo
      </div>
      <Input v-model="title" class="w-full" />
    </div>

    <div class="grid grid-cols-1 gap-6 md:grid-cols-2">
      <!-- Image Upload Section -->
      <div>
        <div class="mb-2 text-2xl font-bold">
          Hình ảnh đại diện
        </div>

        <!-- Image Preview -->
        <div v-if="previewUrl" class="mb-4">
          <div class="relative">
            <img :src="previewUrl" alt="Preview" class="w-full rounded-lg shadow-md">
            <button
              class="absolute right-2 top-2 rounded-full bg-red-500 p-1 text-white transition-colors hover:bg-red-600"
              @click="removeImage"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
          <div class="mt-2 text-sm text-gray-600">
            Kích thước: {{ imageSize }}
          </div>
        </div>

        <!-- Image Upload -->
        <div class="w-full">
          <label
            for="picture"
            class="w-full flex cursor-pointer items-center justify-center border border-gray-300 rounded-lg px-4 py-2 transition-colors hover:bg-gray-300/10"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg" class="mr-2 h-5 w-5" viewBox="0 0 24 24" fill="none"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"
              />
            </svg>
            <span>Chọn hình ảnh</span>
          </label>
          <Input id="picture" type="file" accept="image/*" class="hidden" @change="handleFileChange" />
        </div>
      </div>

      <!-- Video Upload Section -->
      <div>
        <div class="mb-2 text-2xl font-bold">
          Video giới thiệu
        </div>

        <!-- Video Preview -->
        <div v-if="videoPreviewUrl" class="mb-4">
          <div class="relative">
            <video :src="videoPreviewUrl" controls class="w-full rounded-lg shadow-md" />
            <button
              class="absolute right-2 top-2 rounded-full bg-red-500 p-1 text-white transition-colors hover:bg-red-600"
              @click="removeVideo"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
          <div class="mt-2 text-sm text-gray-600">
            Thời lượng: {{ videoDuration }}
          </div>
        </div>

        <!-- Video Upload -->
        <div class="w-full">
          <label
            for="video"
            class="w-full flex cursor-pointer items-center justify-center border border-gray-300 rounded-lg px-4 py-2 transition-colors hover:bg-gray-300/10"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg" class="mr-2 h-5 w-5" viewBox="0 0 24 24" fill="none"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"
              />
            </svg>
            <span>Chọn video</span>
          </label>
          <Input id="video" type="file" accept="video/*" class="hidden" @change="handleVideoChange" />
        </div>
      </div>
    </div>

    <!-- Action Buttons Section -->
    <div class="mt-8 border-t border-gray-200 bg-gray-50 px-4 py-4 -mx-4 dark:border-gray-700 dark:bg-gray-800/50">
      <div class="flex items-center justify-end gap-4">
        <button
          class="rounded-md bg-blue-500 px-4 py-2 text-sm text-white font-medium transition-colors hover:bg-blue-600"
          @click="handleSave"
        >
          Lưu thay đổi
        </button>
        <button
          class="rounded-md bg-red-500 px-4 py-2 text-sm text-white font-medium transition-colors hover:bg-red-600"
          @click="handleDelete"
        >
          Xóa trợ lý ảo
        </button>
      </div>
    </div>
  </div>
</template>
