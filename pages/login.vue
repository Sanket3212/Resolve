<template>
  <div class="min-h-dvh bg-gray-950 text-white flex flex-col items-center justify-center p-6 relative overflow-hidden">
    <div
      class="absolute bg-teal-500/20 w-96 h-96 rounded-full -top-20 -left-20 animate-pulse-slow filter blur-3xl transform will-change-transform"
    ></div>
    <div
      class="absolute bg-green-500/20 w-[500px] h-[500px] rounded-full -bottom-40 -right-20 animate-pulse-slower filter blur-3xl transform will-change-transform"
    ></div>
    <div
      class="absolute bg-purple-500/15 w-80 h-80 rounded-full top-1/4 left-1/4 animate-pulse-slowest filter blur-3xl transform will-change-transform"
    ></div>
    <div
      class="absolute bg-blue-500/10 w-64 h-64 rounded-full bottom-20 left-1/3 animate-pulse-slowest filter blur-3xl transform will-change-transform"
    ></div>

    <div class="w-full max-w-md z-10 animate-fade-in-down">
      <div class="flex justify-center mb-6">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-16 w-16 bg-gradient-to-r from-green-300 to-teal-400 bg-clip-text text-transparent drop-shadow-lg"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          stroke-width="2"
        >
          <path stroke-linecap="round" stroke-linejoin="round" d="M9 17v-5m0 0h5m-5 0l-1-1m1-1l-1-1m-1 1h5m-5 0l-1-1m1-1l-1-1M12 21a9 9 0 110-18 9 9 0 010 18z" />
        </svg>
      </div>

      <form
        @submit.prevent="handleLogin"
        class="bg-black/30 backdrop-blur-xl w-full p-8 rounded-2xl text-white space-y-6 border border-white/10 shadow-2xl"
      >
        <h2
          class="text-4xl font-black text-center bg-gradient-to-r from-green-300 to-teal-400 bg-clip-text text-transparent drop-shadow-lg"
        >
          Sign In
        </h2>
        <p class="text-gray-400 text-center text-lg -mt-3">Welcome back to BugTracker!</p>

        <div class="relative">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.206a8.959 8.959 0 01-4.5 1.207" /></svg>
          </div>
          <input
            type="email"
            v-model="email"
            required
            class="w-full pl-10 pr-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all"
            placeholder="you@example.com"
            autocomplete="email"
          />
        </div>

        <div class="relative">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg>
          </div>
          <input
            :type="showPassword ? 'text' : 'password'"
            v-model="password"
            required
            class="w-full pl-10 pr-10 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all"
            placeholder="Enter your password"
            autocomplete="current-password"
          />
          <button type="button" @click="showPassword = !showPassword" class="absolute inset-y-0 right-0 pr-3 flex items-center text-gray-400 hover:text-green-400 focus:outline-none">
            <svg v-if="!showPassword" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" /></svg>
            <svg v-else class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.542-7 1.274-4.057 5.064-7 9.542-7 .536 0 1.056.052 1.564.148M15 12a3 3 0 11-6 0 3 3 0 016 0z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2 2l20 20" /></svg>
          </button>
        </div>

        <div class="flex justify-between items-center text-sm">
            <label class="flex items-center text-gray-400">
                <input type="checkbox" class="form-checkbox h-4 w-4 text-green-500 rounded border-gray-600 bg-gray-700 focus:ring-green-500 mr-2">
                Remember Me
            </label>
            <NuxtLink to="/forgot-password" class="font-medium text-green-400 hover:text-green-300 transition-colors">Forgot Password?</NuxtLink>
        </div>

        <div v-if="errorMsg" class="text-red-400 text-sm text-center p-3 bg-red-900/20 border border-red-500/30 rounded-lg">
          {{ errorMsg }}
        </div>

        <button
          type="submit"
          :disabled="loading"
          class="w-full inline-flex items-center justify-center gap-2 bg-green-600 hover:bg-green-500 py-3 rounded-xl text-white font-bold text-lg shadow-lg hover:shadow-green-500/30 transition-all duration-300 transform hover:scale-105"
          :class="{ 'opacity-70 cursor-not-allowed': loading }"
        >
          <span v-if="!loading">Sign In</span>
          <span v-else class="flex items-center">
            <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            Signing In...
          </span>
        </button>

        <div class="relative flex py-2 items-center">
          <div class="flex-grow border-t border-white/10"></div>
          <span class="flex-shrink mx-4 text-gray-400">OR</span>
          <div class="flex-grow border-t border-white/10"></div>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <button
            type="button"
            @click="handleSocialLogin('google')"
            class="flex items-center justify-center gap-3 w-full bg-white/5 hover:bg-white/10 py-3 rounded-lg font-semibold text-gray-200 transition-all duration-200 border border-white/10"
          >
            <svg class="w-5 h-5" viewBox="0 0 48 48"><g><path fill="#EA4335" d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"></path><path fill="#4285F4" d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.9-2.16 5.37-4.4 6.98l7.98 6.19c4.63-4.28 7.4-10.75 7.4-17.64z"></path><path fill="#FBBC05" d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"></path><path fill="#34A853" d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.98-6.19c-2.16 1.45-4.92 2.3-8.01 2.3-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"></path><path fill="none" d="M0 0h48v48H0z"></path></g></svg>
            Google
          </button>
          <button
            type="button"
            @click="handleSocialLogin('github')"
            class="flex items-center justify-center gap-3 w-full bg-white/5 hover:bg-white/10 py-3 rounded-lg font-semibold text-gray-200 transition-all duration-200 border border-white/10"
          >
            <svg class="w-5 h-5" viewBox="0 0 16 16"><path fill="currentColor" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"></path></svg>
            GitHub
          </button>
        </div>
      </form>

      <p class="text-center text-gray-400 mt-6 text-md">
        Don't have an account?
        <NuxtLink to="/register" class="text-green-400 hover:text-green-300 font-semibold ml-1 transition-colors duration-200">
          Register Here
        </NuxtLink>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'
import { useRouter } from 'vue-router'

const supabase = useSupabaseClient()
const user = useSupabaseUser()
const router = useRouter()

const email = ref('')
const password = ref('')
const loading = ref(false)
const errorMsg = ref('')
const showPassword = ref(false)

// Redirect if already logged in
watch(user, (currentUser) => {
  if (currentUser) {
    router.push('/profile') // Assuming '/profile' is your dashboard route
  }
}, { immediate: true })

const handleLogin = async () => {
  loading.value = true
  errorMsg.value = '' // Clear previous errors
  try {
    const { error } = await supabase.auth.signInWithPassword({
      email: email.value,
      password: password.value,
    })
    if (error) throw error
  } catch (error) {
    errorMsg.value = error.message
  } finally {
    loading.value = false
  }
}

// Placeholder for social login
const handleSocialLogin = async (provider) => {
  loading.value = true; // Show loading state for social login too
  errorMsg.value = ''; // Clear previous errors
  try {
    const { error } = await supabase.auth.signInWithOAuth({
      provider,
      options: {
        redirectTo: window.location.origin + '/confirm', // Or your desired redirect path after social login
      },
    })
    if (error) throw error;
    // Supabase will redirect the user, so no local router.push here
  } catch (error) {
    errorMsg.value = error.message;
    loading.value = false; // Turn off loading if there's an immediate error
  }
}
</script>

<style scoped>
/* All existing animations and base styles are kept.
   I've updated color classes for consistency with your green/teal theme.
*/
@keyframes fade-in-down {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fade-in-down {
  animation: fade-in-down 0.6s ease-out forwards;
}

/* Background pulse animations (consistent with index page) */
@keyframes pulse-slow {
  0%, 100% {
    transform: scale(1) translateX(0);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.1) translateX(10px);
    opacity: 0.3;
  }
}

@keyframes pulse-slower {
  0%, 100% {
    transform: scale(1) translateY(0);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.05) translateY(-10px);
    opacity: 0.25;
  }
}

@keyframes pulse-slowest {
  0%, 100% {
    transform: scale(1) translateX(0) translateY(0);
    opacity: 0.15;
  }
  50% {
    transform: scale(1.03) translateX(-5px) translateY(5px);
    opacity: 0.2;
  }
}

.animate-pulse-slow {
  animation: pulse-slow 12s infinite ease-in-out;
}
.animate-pulse-slower {
  animation: pulse-slower 18s infinite ease-in-out;
}
.animate-pulse-slowest {
  animation: pulse-slowest 24s infinite ease-in-out;
}

/* Custom shadow for consistency with index page */
.shadow-2xl {
  box-shadow: 0 25px 50px -12px rgba(0, 255, 204, 0.25), 0 10px 10px -5px rgba(0, 255, 204, 0.1);
}
</style>