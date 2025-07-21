<template>
  <div class="min-h-dvh bg-gray-950 text-white flex items-center justify-center p-6 relative overflow-hidden">
    <div
      class="absolute bg-teal-500/20 w-96 h-96 rounded-full -top-20 -left-20 animate-pulse-slow filter blur-3xl"
    ></div>
    <div
      class="absolute bg-green-500/20 w-[500px] h-[500px] rounded-full -bottom-40 -right-20 animate-pulse-slower filter blur-3xl"
    ></div>
    <div
      class="absolute bg-purple-500/15 w-80 h-80 rounded-full top-1/4 left-1/4 animate-pulse-slowest filter blur-3xl"
    ></div>

    <div
      class="w-full max-w-md bg-black/30 backdrop-blur-xl border border-white/10 rounded-2xl p-8 sm:p-10 shadow-2xl z-20 animate-fade-in"
    >
      <div class="text-center mb-8">
        <h1
          class="text-4xl md:text-5xl font-black mb-2 bg-gradient-to-r from-green-300 to-teal-400 bg-clip-text text-transparent drop-shadow-lg"
        >
          Create Account
        </h1>
        <p class="text-gray-400 text-lg">Join BugTracker today!</p>
      </div>

      <form @submit.prevent="handleRegister" class="space-y-6">
        <div>
          <label for="email" class="block text-gray-300 text-sm font-medium mb-2">Email Address</label>
          <div class="relative">
            <input
              type="email"
              id="email"
              v-model="email"
              required
              placeholder="you@example.com"
              class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all duration-200 pl-10"
              :class="{ 'border-red-500': showValidationError && !email }"
            />
            <svg
              class="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.5a2.5 2.5 0 005 0V12"
              ></path>
            </svg>
          </div>
        </div>

        <div>
          <label for="password" class="block text-gray-300 text-sm font-medium mb-2">Password</label>
          <div class="relative">
            <input
              :type="passwordVisible ? 'text' : 'password'"
              id="password"
              v-model="password"
              required
              minlength="6"
              placeholder="••••••••"
              class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all duration-200 pl-10 pr-10"
              :class="{ 'border-red-500': showValidationError && (!password || password.length < 6) }"
            />
            <svg
              class="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-gray-400"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v3h8z"
              ></path>
            </svg>
            <button
              type="button"
              @click="togglePasswordVisibility"
              class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-green-400 focus:outline-none"
              aria-label="Toggle password visibility"
            >
              <svg
                v-if="passwordVisible"
                class="h-5 w-5"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-2.18M21 12c-1.894 3.106-5.594 5-9 5a10.076 10.076 0 01-2.015-.224m-2.126-2.126a1.5 1.5 0 01-2.015-2.015m5.414 5.414L21 3"
                ></path>
              </svg>
              <svg
                v-else
                class="h-5 w-5"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
                ></path>
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"
                ></path>
              </svg>
            </button>
          </div>
        </div>

        <div>
          <label for="role" class="block text-gray-300 text-sm font-medium mb-2">Select Your Role</label>
          <div class="relative">
            <select
              id="role"
              v-model="role"
              class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white appearance-none focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent transition-all duration-200 pr-10"
            >
              <option
                v-for="r in roles"
                :key="r"
                :value="r"
                class="bg-gray-800 text-white hover:bg-green-700"
              >
                {{ r.charAt(0).toUpperCase() + r.slice(1) }}
              </option>
            </select>
            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-400">
              <svg
                class="fill-current h-4 w-4"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 6.757 7.586 5.343 9z" />
              </svg>
            </div>
          </div>
          <p class="text-gray-500 text-xs mt-1">Choose the role that best describes your function in the team.</p>
        </div>

        <div v-if="errorMsg" class="text-red-400 text-sm text-center bg-red-900/20 border border-red-500/30 p-3 rounded-lg">
          {{ errorMsg }}
        </div>

        <button
          type="submit"
          :disabled="loading"
          class="w-full inline-flex items-center justify-center gap-2 bg-green-600 hover:bg-green-500 py-3 rounded-xl text-white font-bold text-lg shadow-lg hover:shadow-green-500/30 transition-all duration-300 transform hover:scale-105"
          :class="{ 'opacity-70 cursor-not-allowed': loading }"
        >
          <span v-if="!loading">Register Account</span>
          <span v-else class="flex items-center">
            <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            Processing...
          </span>
        </button>
      </form>

      <p class="text-center text-gray-400 mt-6 text-md">
        Already have an account?
        <NuxtLink to="/login" class="text-green-400 hover:text-green-300 font-semibold ml-1 transition-colors duration-200">
          Sign In
        </NuxtLink>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useSupabaseClient } from '#imports'
import { useRouter } from 'vue-router'

const supabase = useSupabaseClient()
const router = useRouter()

const email = ref('')
const password = ref('')
const role = ref('tester') // default role
const loading = ref(false)
const errorMsg = ref('')
const passwordVisible = ref(false) // State for password visibility
const showValidationError = ref(false) // New state for showing validation errors on fields

const roles = [ 'tester', 'developer']

const togglePasswordVisibility = () => {
  passwordVisible.value = !passwordVisible.value
}

const handleRegister = async () => {
  errorMsg.value = ''
  showValidationError.value = true // Activate validation display on submit attempt
  loading.value = true

  // Client-side validation
  if (!email.value || !password.value || password.value.length < 6) {
    errorMsg.value = 'Please enter a valid email and a password of at least 6 characters.'
    loading.value = false
    return
  }

  // 1. Sign up user in Supabase Auth
  const { data: signUpData, error: signUpError } = await supabase.auth.signUp({
    email: email.value,
    password: password.value,
  })

  if (signUpError) {
    errorMsg.value = signUpError.message
    loading.value = false
    return
  }

  // 2. Insert into profiles table with chosen role
  const userId = signUpData.user?.id

  if (!userId) {
    errorMsg.value = 'User ID not found after sign-up. Please try again.'
    loading.value = false
    return
  }

  const { error: profileError } = await supabase.from('profiles').upsert({
    id: userId,
    role: role.value,
    updated_at: new Date(),
  })

  if (profileError) {
    // If profile insertion fails, you might want to consider deleting the user from auth
    // to prevent orphaned accounts, but for a simple demo, just log/display
    console.error('Error inserting profile:', profileError.message);
    errorMsg.value = 'Registration successful, but profile could not be saved. Please contact support.'
    loading.value = false
    return
  }

  alert('Registration successful! Please verify your email before logging in. Check your inbox and spam folder.')
  router.push('/login')
  loading.value = false
}
</script>

<style>
/* Add these animations to your global CSS or in the style tag for consistency */
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fade-in 0.6s ease-out forwards;
  opacity: 0; /* Start hidden */
}

/* Background pulse animations (copied from your index page) */
@keyframes pulse-slow {
  0%, 100% {
    transform: scale(1);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.3;
  }
}

@keyframes pulse-slower {
  0%, 100% {
    transform: scale(1);
    opacity: 0.2;
  }
  50% {
    transform: scale(1.05);
    opacity: 0.25;
  }
}

@keyframes pulse-slowest {
  0%, 100% {
    transform: scale(1);
    opacity: 0.15;
  }
  50% {
    transform: scale(1.03);
    opacity: 0.2;
  }
}

.animate-pulse-slow {
  animation: pulse-slow 10s infinite ease-in-out;
}
.animate-pulse-slower {
  animation: pulse-slower 15s infinite ease-in-out;
}
.animate-pulse-slowest {
  animation: pulse-slowest 20s infinite ease-in-out;
}
</style>