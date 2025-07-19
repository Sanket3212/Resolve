<template>
  <div class="min-h-dvh bg-gray-950 text-white flex items-center justify-center p-6 relative">
    
    <div
      class="absolute bg-teal-500/20 w-96 h-96 rounded-full -top-20 -left-20 animate-pulse-slow filter blur-3xl"
    ></div>
    <div
      class="absolute bg-green-500/20 w-[500px] h-[500px] rounded-full -bottom-40 -right-20 animate-pulse-slower filter blur-3xl"
    ></div>

    <div
      class="pointer-events-none fixed w-64 h-64 bg-green-500 opacity-20 rounded-full blur-3xl transform -translate-x-1/2 -translate-y-1/2 z-10"
      :style="{ top: `${cursorY}px`, left: `${cursorX}px` }"
    />

    <div
      class="w-full max-w-2xl text-center bg-black/20 backdrop-blur-lg border border-white/10 rounded-2xl p-8 sm:p-12 shadow-2xl z-20"
    >
      <h1
        class="text-5xl md:text-6xl font-black mb-4 bg-gradient-to-r from-green-300 to-teal-400 bg-clip-text text-transparent animate-fade-in-up"
        style="animation-delay: 0.2s"
      >
        Welcome to BugTracker
      </h1>
      <p
        class="text-lg md:text-xl mb-10 text-gray-300 animate-fade-in-up"
        style="animation-delay: 0.4s"
      >
        A seamless, role-based dashboard system built with Supabase and Nuxt 3.
      </p>
      <NuxtLink
        to="/login"
        class="inline-flex items-center gap-2 bg-green-500 hover:bg-green-400 transition-all duration-300 px-8 py-4 rounded-xl font-bold text-gray-900 text-lg shadow-lg hover:shadow-green-500/30 hover:scale-105 animate-fade-in-up"
        style="animation-delay: 0.6s"
      >
        <span>Get Started</span>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2.5"
            d="M17 8l4 4m0 0l-4 4m4-4H3"
          />
        </svg>
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const cursorX = ref(0)
const cursorY = ref(0)

const updateCursor = (e) => {
  cursorX.value = e.clientX
  cursorY.value = e.clientY
}

onMounted(() => {
  window.addEventListener('mousemove', updateCursor)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', updateCursor)
})
</script>

<style>
/* Add these animations to your global CSS or in the style tag */
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in-up {
  animation: fade-in-up 0.8s ease-out forwards;
  opacity: 0; /* Start hidden */
}

@keyframes pulse-slow {
  0%,
  100% {
    transform: scale(1);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.3;
  }
}

@keyframes pulse-slower {
  0%,
  100% {
    transform: scale(1);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.05);
    opacity: 0.25;
  }
}

.animate-pulse-slow {
  animation: pulse-slow 10s infinite ease-in-out;
}
.animate-pulse-slower {
  animation: pulse-slower 15s infinite ease-in-out;
}
</style>