<template>
  <div class="relative min-h-screen bg-[#0D0D0D] text-white overflow-x-hidden">
    <!-- Animated background blobs -->
    <div class="absolute bg-teal-400/10 w-72 h-72 rounded-full -top-16 -left-16 animate-pulse-slow blur-3xl" />
    <div class="absolute bg-green-500/10 w-96 h-96 rounded-full -top-20 -right-32 animate-pulse-slow blur-3xl" />
    <div class="absolute bg-teal-300/10 w-72 h-72 rounded-full -bottom-16 left-1/3 animate-pulse-slow blur-3xl" />

    <!-- Hero Section -->
    <div class="relative z-10 flex flex-col items-center justify-center px-6 pt-24 text-center space-y-8">
      <h1 class="text-4xl md:text-6xl lg:text-7xl font-extrabold bg-gradient-to-r from-green-300 to-teal-400 bg-clip-text text-transparent">
        Effortless Bug Tracking.<br class="hidden sm:inline" /> Seamless Project Flow.
      </h1>
      <p class="max-w-2xl text-lg md:text-xl text-gray-300">
        BugTracker is your team’s all-in-one solution for capturing, tracking, and squashing bugs—fast.
      </p>
      <div class="flex flex-wrap justify-center gap-4">
        <NuxtLink
          to="/register"
          class="inline-flex items-center justify-center gap-2 bg-green-500 hover:bg-green-400 transition-all duration-300 px-10 py-5 rounded-xl font-bold text-gray-900 text-xl shadow-lg hover:shadow-green-500/30 hover:scale-105 group hover:-translate-y-0.5"
        >
          Get Started
          <Icon name="carbon:arrow-right" class="w-5 h-5 group-hover:translate-x-1 transition-transform" />
        </NuxtLink>
        <NuxtLink
          to="/login"
          class="inline-flex items-center justify-center gap-2 border border-white/10 bg-white/5 hover:bg-white/10 px-10 py-5 rounded-xl font-semibold text-white text-xl hover:scale-105 transition-all"
        >
          Login
        </NuxtLink>
      </div>
    </div>

    <!-- Feature Cards -->
    <div class="relative z-10 mt-32 px-6 max-w-6xl mx-auto grid md:grid-cols-3 gap-8">
  <!-- Card 1: Dashboard -->
  <div class="p-6 bg-gradient-to-br from-gray-900/40 to-black/10 rounded-2xl ring-1 ring-inset ring-white/10 transition-all hover:ring-green-400/20 hover:scale-[1.02] shadow-xl backdrop-blur-md">
    <div class="flex items-center space-x-4 mb-4">
      <div class="w-12 h-12 bg-green-400/10 rounded-full flex items-center justify-center">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 3v6h-6V3h6zM20.25 3v6h-6V3h6zM20.25 14.25v6h-6v-6h6zM9.75 14.25v6h-6v-6h6z" />
        </svg>
      </div>
      <h3 class="text-xl font-semibold text-teal-300">Intuitive Dashboard</h3>
    </div>
    <p class="text-sm text-gray-300 leading-relaxed">Visually track progress, assign issues, and get a clear overview of your project at a glance.</p>
  </div>

  <!-- Card 2: Roles -->
  <div class="p-6 bg-gradient-to-br from-gray-900/40 to-black/10 rounded-2xl ring-1 ring-inset ring-white/10 transition-all hover:ring-green-400/20 hover:scale-[1.02] shadow-xl backdrop-blur-md">
    <div class="flex items-center space-x-4 mb-4">
      <div class="w-12 h-12 bg-green-400/10 rounded-full flex items-center justify-center">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-3-3h-2m-4 5h-4v-2a4 4 0 014-4h0a4 4 0 014 4v2zM6 20h5v-2a3 3 0 00-3-3H6a3 3 0 00-3 3v2zM15 11a4 4 0 10-8 0 4 4 0 008 0z" />
        </svg>
      </div>
      <h3 class="text-xl font-semibold text-teal-300">Role-Based Access</h3>
    </div>
    <p class="text-sm text-gray-300 leading-relaxed">Custom dashboards and permissions for developers, testers, and project admins.</p>
  </div>

  <!-- Card 3: Reports -->
  <div class="p-6 bg-gradient-to-br from-gray-900/40 to-black/10 rounded-2xl ring-1 ring-inset ring-white/10 transition-all hover:ring-green-400/20 hover:scale-[1.02] shadow-xl backdrop-blur-md">
    <div class="flex items-center space-x-4 mb-4">
      <div class="w-12 h-12 bg-green-400/10 rounded-full flex items-center justify-center">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 17v-2a4 4 0 018 0v2m1 0a5 5 0 00-10 0v2h10v-2zM12 4v4m0 0l3-3m-3 3l-3-3" />
        </svg>
      </div>
      <h3 class="text-xl font-semibold text-teal-300">Smart Reports</h3>
    </div>
    <p class="text-sm text-gray-300 leading-relaxed">Generate real-time reports to gain insights and prioritize what matters most.</p>
  </div>
</div>


    <!-- Cursor Circle Effect -->
    <div id="cursor" class="fixed w-24 h-24 rounded-full pointer-events-none z-50 bg-gradient-to-r from-green-500 via-teal-400 to-green-400 opacity-30 blur-2xl transition-transform duration-300"></div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';

onMounted(() => {
  const cursor = document.getElementById('cursor');
  window.addEventListener('mousemove', (e) => {
    if (cursor) {
      cursor.style.transform = `translate(${e.clientX - 48}px, ${e.clientY - 48}px)`;
    }
  });
});
</script>
