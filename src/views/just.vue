<template>
  <div
    :class="[
      'flex flex-col items-center justify-center p-6 rounded-2xl transition-all duration-500',
      isLight ? 'bg-gray-700' : 'bg-white'
    ]"
  >
    <img
      ref="logoRef"
      :src="logoPath"
      alt="Company Logo"
      class="h-24 w-24 rounded-full object-contain border"
      @load="analyzeLogo"
    />
    <p class="mt-3 font-semibold text-gray-800">Brightness: {{ brightness.toFixed(2) }}</p>
    <p class="text-sm text-gray-500">Detected background → {{ isLight ? 'Gray (Light Logo)' : 'White (Dark Logo)' }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// 👇 Replace with your image path
const logoPath = './image.png'  // make sure this file exists in your `public/auth` folder

const logoRef = ref(null)
const brightness = ref(0)
const isLight = ref(false)

function analyzeLogo() {
  const img = logoRef.value
  const canvas = document.createElement('canvas')
  const ctx = canvas.getContext('2d')

  canvas.width = img.naturalWidth
  canvas.height = img.naturalHeight
  ctx.drawImage(img, 0, 0, img.naturalWidth, img.naturalHeight)

  const imageData = ctx.getImageData(0, 0, img.naturalWidth, img.naturalHeight)
  let r = 0, g = 0, b = 0
  const totalPixels = imageData.data.length / 4

  for (let i = 0; i < imageData.data.length; i += 4) {
    r += imageData.data[i]
    g += imageData.data[i + 1]
    b += imageData.data[i + 2]
  }

  r /= totalPixels
  g /= totalPixels
  b /= totalPixels

  brightness.value = (r * 299 + g * 587 + b * 114) / 1000
  isLight.value = brightness.value > 150

  console.log('🔹 Avg Brightness:', brightness.value)
}
</script>
