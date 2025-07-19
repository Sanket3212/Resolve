<script setup>
import { ref, onMounted, watch } from 'vue'
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
    router.push('/profile')
  }
}, { immediate: true })


const handleLogin = async () => {
  loading.value = true
  errorMsg.value = ''
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
    const { error } = await supabase.auth.signInWithOAuth({ provider })
    if (error) {
        errorMsg.value = error.message
    }
}
</script>

<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-slate-900 px-4 relative overflow-hidden">
    <div class="absolute top-0 left-0 w-96 h-96 bg-emerald-500/20 rounded-full filter blur-3xl opacity-50 animate-pulse"></div>
    <div class="absolute bottom-0 right-0 w-96 h-96 bg-sky-500/20 rounded-full filter blur-3xl opacity-50 animate-pulse-slow"></div>

    <div class="w-full max-w-md z-10 animate-fade-in-down">
      <div class="flex justify-center mb-6">
        <svg class="w-16 h-16 text-emerald-400" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
      
      <form
        @submit.prevent="handleLogin"
        class="bg-slate-800/50 backdrop-blur-lg w-full p-8 rounded-2xl text-white space-y-6 border border-slate-700"
      >
        <h2 class="text-3xl font-bold text-center text-white">Sign in</h2>

        <div class="relative">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.206a8.959 8.959 0 01-4.5 1.207" /></svg>
          </div>
          <input
            type="email"
            v-model="email"
            required
            class="w-full pl-10 pr-4 py-3 bg-slate-700 border border-slate-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 transition-all"
            placeholder="you@example.com"
            autocomplete="email"
          />
        </div>

        <div class="relative">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg>
          </div>
          <input
            :type="showPassword ? 'text' : 'password'"
            v-model="password"
            required
            class="w-full pl-10 pr-10 py-3 bg-slate-700 border border-slate-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 transition-all"
            placeholder="Enter your password"
            autocomplete="current-password"
          />
          <button type="button" @click="showPassword = !showPassword" class="absolute inset-y-0 right-0 pr-3 flex items-center">
             <svg v-if="!showPassword" class="w-5 h-5 text-slate-400 hover:text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" /></svg>
             <svg v-else class="w-5 h-5 text-slate-400 hover:text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.542-7 1.274-4.057 5.064-7 9.542-7 .536 0 1.056.052 1.564.148M15 12a3 3 0 11-6 0 3 3 0 016 0z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2 2l20 20" /></svg>
          </button>
        </div>

        <div class="flex justify-end text-sm">
            <a href="#" class="font-medium text-emerald-400 hover:underline">Forgot Password?</a>
        </div>

        <div v-if="errorMsg" class="text-red-400 text-sm text-center p-3 bg-red-500/10 rounded-lg">
          {{ errorMsg }}
        </div>

        <button
          type="submit"
          :disabled="loading"
          class="w-full bg-emerald-500 hover:bg-emerald-600 disabled:bg-slate-600 py-3 rounded-lg font-bold uppercase tracking-wider transition-all duration-300 transform hover:scale-105"
        >
          {{ loading ? 'Signing in...' : 'Sign In' }}
        </button>

        <div class="relative flex py-2 items-center">
            <div class="flex-grow border-t border-slate-600"></div>
            <span class="flex-shrink mx-4 text-slate-400">OR</span>
            <div class="flex-grow border-t border-slate-600"></div>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button type="button" @click="handleSocialLogin('google')" class="flex items-center justify-center gap-2 w-full bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-semibold transition-all">
                <svg class="w-5 h-5" viewBox="0 0 48 48"><g><path fill="#EA4335" d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"></path><path fill="#4285F4" d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.9-2.16 5.37-4.4 6.98l7.98 6.19c4.63-4.28 7.4-10.75 7.4-17.64z"></path><path fill="#FBBC05" d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"></path><path fill="#34A853" d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.98-6.19c-2.16 1.45-4.92 2.3-8.01 2.3-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"></path><path fill="none" d="M0 0h48v48H0z"></path></g></svg>
                Google
            </button>
            <button type="button" @click="handleSocialLogin('github')" class="flex items-center justify-center gap-2 w-full bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-semibold transition-all">
                <svg class="w-5 h-5" viewBox="0 0 16 16"><path fill="currentColor" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"></path></svg>
                GitHub
            </button>
        </div>
      </form>
       <div class="text-center text-sm text-slate-400 mt-6">
            Don't have an account?
            <NuxtLink to="/register" class="font-medium text-emerald-400 hover:underline">Sign up</NuxtLink>
        </div>
    </div>
  </div>
</template>

<style scoped>
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

@keyframes pulse-slow {
  50% {
    opacity: 0.75;
    transform: scale(1.1);
  }
}
.animate-pulse-slow {
    animation: pulse-slow 8s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}
</style>