<!-- /pages/index.vue -->
<template>
  <div
    class="min-h-screen bg-gradient-to-br from-gray-900 via-black to-gray-800 text-white flex flex-col items-center justify-center relative overflow-hidden"
  >
    <!-- Custom Cursor Glow -->
    <div
      class="pointer-events-none fixed w-40 h-40 bg-green-500 opacity-30 rounded-full blur-3xl transform -translate-x-1/2 -translate-y-1/2 z-10"
      :style="{ top: `${cursorY}px`, left: `${cursorX}px` }"
    />

    <div class="z-20 text-center px-4">
      <h1 class="text-5xl font-extrabold mb-4 bg-gradient-to-r from-green-400 to-teal-500 bg-clip-text text-transparent">
        Welcome to BugTracker
      </h1>
      <p class="text-lg mb-8 text-gray-300">
        A role-based dashboard system using Supabase and Nuxt 3
      </p>
      <NuxtLink
        to="/login"
        class="bg-green-600 hover:bg-green-700 transition px-6 py-3 rounded-xl font-semibold text-white"
      >
        Get Started
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const cursorX = ref(0)
const cursorY = ref(0)

onMounted(() => {
  window.addEventListener('mousemove', (e) => {
    cursorX.value = e.clientX
    cursorY.value = e.clientY
  })
})
</script>
