<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-900 px-4">
    <div class="w-full max-w-md">
      <form @submit.prevent="handleRegister" class="bg-gray-800 p-8 rounded-xl shadow-lg text-white space-y-6 border border-gray-700">
        <div class="text-center">
            <h2 class="text-3xl font-bold">Create Account</h2>
            <p class="text-gray-400 mt-1">Join BugTracker to get started.</p>
        </div>

        <div>
          <label for="email" class="block text-sm font-medium mb-1 text-gray-300">Email</label>
          <input
            id="email"
            type="email"
            v-model="email"
            required
            class="w-full px-4 py-2 rounded-lg bg-gray-700 border border-gray-600 focus:outline-none focus:ring-2 focus:ring-green-500 transition"
            placeholder="you@example.com"
          />
        </div>

        <div>
          <label for="password" class="block text-sm font-medium mb-1 text-gray-300">Password</label>
          <input
            id="password"
            type="password"
            v-model="password"
            required
            minlength="6"
            class="w-full px-4 py-2 rounded-lg bg-gray-700 border border-gray-600 focus:outline-none focus:ring-2 focus:ring-green-500 transition"
            placeholder="••••••••"
          />
        </div>

        <div>
          <label for="role" class="block text-sm font-medium mb-1 text-gray-300">Your Role</label>
          <select
            id="role"
            v-model="role"
            class="w-full px-4 py-2 rounded-lg bg-gray-700 border border-gray-600 focus:outline-none focus:ring-2 focus:ring-green-500 transition"
          >
            <option v-for="r in roles" :key="r" :value="r">
              {{ r.charAt(0).toUpperCase() + r.slice(1) }}
            </option>
          </select>
        </div>

        <div v-if="errorMsg" class="text-red-400 text-sm text-center p-3 bg-red-500/10 rounded-lg">
          {{ errorMsg }}
        </div>
         <div v-if="successMsg" class="text-green-400 text-sm text-center p-3 bg-green-500/10 rounded-lg">
          {{ successMsg }}
        </div>

        <button
          type="submit"
          :disabled="loading"
          class="w-full bg-green-600 hover:bg-green-500 disabled:bg-gray-500 py-3 rounded-lg text-white font-semibold transition-all duration-300 flex items-center justify-center"
        >
          <svg v-if="loading" class="animate-spin h-5 w-5 mr-3" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
          <span>{{ loading ? 'Registering...' : 'Register' }}</span>
        </button>

         <div class="text-center text-sm text-gray-400">
            Already have an account?
            <NuxtLink to="/login" class="font-medium text-green-400 hover:underline">Log in</NuxtLink>
        </div>
      </form>
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
const successMsg = ref('')

const roles = ['tester', 'developer', 'admin']

const handleRegister = async () => {
  errorMsg.value = ''
  successMsg.value = ''
  loading.value = true

  try {
    // 1. Sign up user in Supabase Auth
    const { data: signUpData, error: signUpError } = await supabase.auth.signUp({
      email: email.value,
      password: password.value,
    })

    if (signUpError) throw signUpError

    // 2. Insert into profiles table with chosen role
    const userId = signUpData.user?.id
    if (!userId) throw new Error('User ID not found after sign-up.')

    const { error: profileError } = await supabase.from('profiles').upsert({
      id: userId,
      role: role.value,
      email: email.value, // It's good practice to store email in profiles too
      updated_at: new Date(),
    })

    if (profileError) throw profileError

    // Show success message and redirect
    successMsg.value = 'Registration successful! Please check your email to verify your account.'
    setTimeout(() => {
        router.push('/login')
    }, 3000) // Wait 3 seconds before redirecting

  } catch (error) {
    errorMsg.value = error.message
  } finally {
    loading.value = false
  }
}
</script>