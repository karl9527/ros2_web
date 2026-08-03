<template>
  <div class="card camera-card">
    <div class="card-header">
      摄像头
    </div>
    <div class="card-body camera-body">
      <div class="camera-frame">
        <img
          v-if="activeUrl"
          :key="imageKey"
          class="camera-stream"
          :src="displayUrl"
          alt="摄像头画面"
          @load="streamLoaded = true"
          @error="streamLoaded = false"
        >
        <div v-if="!streamLoaded" class="camera-placeholder">
          {{ cameraMessage }}
        </div>
      </div>

      <div class="camera-controls">
        <input
          v-model.trim="draftUrl"
          class="url-input camera-url-input"
          type="text"
          placeholder="http://摄像头地址"
          @keyup.enter="applyUrl"
        >
        <button class="btn camera-btn" type="button" @click="applyUrl">
          应用
        </button>
        <button class="btn camera-btn camera-btn-secondary" type="button" @click="reloadStream">
          刷新
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  defaultUrl: {
    type: String,
    default: ''
  }
})

const draftUrl = ref(props.defaultUrl)
const activeUrl = ref(props.defaultUrl)
const imageKey = ref(0)
const streamLoaded = ref(false)
const cameraMessage = ref('等待摄像头画面...')

const displayUrl = computed(() => {
  if (!activeUrl.value) return ''
  const separator = activeUrl.value.includes('?') ? '&' : '?'
  return `${activeUrl.value}${separator}_=${imageKey.value}`
})

function applyUrl() {
  activeUrl.value = draftUrl.value
  streamLoaded.value = false
  cameraMessage.value = '等待摄像头画面...'
  imageKey.value += 1
}

function reloadStream() {
  streamLoaded.value = false
  cameraMessage.value = '等待摄像头画面...'
  imageKey.value += 1
}

watch(() => props.defaultUrl, (url) => {
  if (!url || activeUrl.value === url) return
  draftUrl.value = url
  activeUrl.value = url
  streamLoaded.value = false
  cameraMessage.value = '等待摄像头画面...'
  imageKey.value += 1
})
</script>
