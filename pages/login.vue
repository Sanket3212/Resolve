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

// Redirect if already logged in
let redirected = false

onMounted(() => {
  if (user.value && !redirected) {
    redirected = true
    router.push('/profile')
  }
})

watch(user, (u) => {
  if (u && !redirected) {
    redirected = true
    router.push('/profile')
  }
})


const handleLogin = async () => {
  loading.value = true
  errorMsg.value = ''

  const { error } = await supabase.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  })

  if (error) {
    errorMsg.value = error.message
  }

  loading.value = false
}
</script>

<style scoped>
form {
  box-shadow:
    0 4px 6px rgba(0, 197, 142, 0.3),
    0 1px 3px rgba(0, 0, 0, 0.2);
  transition: box-shadow 0.3s ease;
}

form:hover {
  box-shadow:
    0 10px 15px rgba(0, 197, 142, 0.5),
    0 4px 6px rgba(0, 0, 0, 0.3);
}

input {
  transition: box-shadow 0.3s ease, border-color 0.3s ease;
  border-radius: 0.5rem; /* 8px */
}

input:focus {
  outline: none;
  border-color: #00c58e;
  box-shadow: 0 0 8px 2px rgba(0, 197, 142, 0.6);
  background-color: #2c3a52;
}

button {
  box-shadow: 0 4px 8px rgba(0, 197, 142, 0.4);
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
  border-radius: 0.375rem; /* 6px */
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #00b27a;
  box-shadow: 0 6px 12px rgba(0, 197, 142, 0.7);
}
</style>

<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-[#0f172a] px-4">
    <form
      @submit.prevent="handleLogin"
      class="bg-[#1e293b] w-full max-w-md p-10 rounded-xl text-white space-y-6"
    >
      <h2 class="text-4xl font-extrabold text-white text-center mb-8">Login</h2>

      <div>
        <label class="block text-sm mb-2 text-[#94a3b8] font-semibold">Email</label>
        <input
          type="email"
          v-model="email"
          required
          class="w-full px-5 py-3 bg-[#334155] border border-[#475569] text-white placeholder:text-[#94a3b8] font-medium"
          placeholder="you@example.com"
          autocomplete="email"
          spellcheck="false"
        />
      </div>

      <div>
        <label class="block text-sm mb-2 text-[#94a3b8] font-semibold">Password</label>
        <input
          type="password"
          v-model="password"
          required
          class="w-full px-5 py-3 bg-[#334155] border border-[#475569] text-white placeholder:text-[#94a3b8] font-medium"
          placeholder="Enter your password"
          autocomplete="current-password"
          spellcheck="false"
        />
      </div>

      <div v-if="errorMsg" class="text-red-500 text-center font-semibold text-sm">
        {{ errorMsg }}
      </div>

      <button
        type="submit"
        :disabled="loading"
        class="w-full bg-[#00c58e] py-3 text-lg font-bold text-white uppercase tracking-wide shadow-md"
      >
        {{ loading ? 'Logging in...' : 'Login' }}
      </button>
    </form>

    <p class="mt-6 text-[#94a3b8] text-center font-medium">
      Don't have an account?
      <router-link
        to="/register"
        class="text-[#00c58e] hover:underline ml-1 font-semibold"
      >
        Register
      </router-link>
    </p>
  </div>
</template>
