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

const roles = ['admin', 'tester', 'developer']

const handleRegister = async () => {
  errorMsg.value = ''
  loading.value = true

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
    errorMsg.value = 'User ID not found after sign-up.'
    loading.value = false
    return
  }

  const { error: profileError } = await supabase.from('profiles').upsert({
    id: userId,
    role: role.value,
    updated_at: new Date(),
  })

  if (profileError) {
    errorMsg.value = profileError.message
    loading.value = false
    return
  }

  alert('Registration successful! Please verify your email before logging in.')
  router.push('/login')
  loading.value = false
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-900 px-4">
    <form
      @submit.prevent="handleRegister"
      class="bg-gray-800 w-full max-w-md p-8 rounded-xl shadow-lg text-white space-y-6"
    >
      <h2 class="text-2xl font-bold text-center">Register</h2>

      <div>
        <label class="block text-sm mb-1">Email</label>
        <input
          type="email"
          v-model="email"
          required
          class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
        />
      </div>

      <div>
        <label class="block text-sm mb-1">Password</label>
        <input
          type="password"
          v-model="password"
          required
          minlength="6"
          class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
        />
      </div>

      <div>
        <label class="block text-sm mb-1">Role</label>
        <select
          v-model="role"
          class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
        >
          <option v-for="r in roles" :key="r" :value="r">
            {{ r.charAt(0).toUpperCase() + r.slice(1) }}
          </option>
        </select>
      </div>

      <div v-if="errorMsg" class="text-red-400 text-sm text-center">
        {{ errorMsg }}
      </div>

      <button
        type="submit"
        :disabled="loading"
        class="w-full bg-green-600 hover:bg-green-700 py-2 rounded text-white font-semibold"
      >
        {{ loading ? 'Registering...' : 'Register' }}
      </button>
    </form>
  </div>
</template>
