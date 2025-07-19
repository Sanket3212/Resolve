<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import { useSupabaseClient, useSupabaseUser } from '#imports'

// Import dashboard components
import AdminDashboard from '@/components/AdminDashboard.vue'
import TesterDashboard from '@/components/TesterDashboard.vue'
import DeveloperDashboard from '@/components/DeveloperDashboard.vue'

const supabase = useSupabaseClient()
const user = useSupabaseUser()
const router = useRouter()

const loading = ref(true)
const profile = ref(null)
const fetchError = ref(null) // New state for handling fetch errors

// Redirect to login if no user is found
onMounted(() => {
  if (!user.value) {
    router.push('/login')
  } else {
    fetchProfile()
  }
})

async function fetchProfile() {
  try {
    const { data, error } = await supabase
      .from('profiles')
      .select('id, name, email, role')
      .eq('id', user.value.id)
      .single()

    if (error) throw error // Throw error to be caught by the catch block

    profile.value = data
  } catch (error) {
    console.error('Error fetching user profile:', error.message)
    // Set a user-friendly error message
    fetchError.value = 'Could not load your profile. Please try refreshing the page.'
  } finally {
    loading.value = false
  }
}

// Cleaner way to select the dashboard based on role
const dashboardComponent = computed(() => {
  switch (profile.value?.role) {
    case 'admin':
      return AdminDashboard
    case 'tester':
      return TesterDashboard
    case 'developer':
      return DeveloperDashboard
    default:
      return null
  }
})

const handleLogout = async () => {
  await supabase.auth.signOut()
  router.push('/login')
}
</script>

<template>
  <div class="min-h-screen bg-gray-900 text-white">
    <header
      class="sticky top-0 z-50 flex items-center justify-between border-b border-gray-700 bg-gray-900 px-6 py-4"
    >
      <div>
        <div v-if="loading" class="animate-pulse space-y-2">
          <div class="h-6 w-48 rounded bg-gray-700"></div>
          <div class="h-4 w-32 rounded bg-gray-700"></div>
        </div>
        <div v-else-if="profile">
          <h1 class="text-xl font-bold">Welcome, {{ profile.name }}</h1>
          <p class="text-sm capitalize text-gray-400">
            Role: <strong>{{ profile.role }}</strong>
          </p>
        </div>
      </div>
      <button
        @click="handleLogout"
        class="flex items-center gap-2 rounded bg-red-600 px-4 py-2 text-white transition-colors hover:bg-red-700"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-5 w-5"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
          />
        </svg>
        Logout
      </button>
    </header>

    <main class="p-6">
      <div v-if="loading" class="flex justify-center items-center pt-24">
        <svg
          class="animate-spin h-10 w-10 text-white"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            class="opacity-25"
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            stroke-width="4"
          ></circle>
          <path
            class="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
          ></path>
        </svg>
      </div>

      <div v-else-if="fetchError" class="text-center pt-24 text-red-400">
        <p class="text-xl font-semibold">Something went wrong</p>
        <p>{{ fetchError }}</p>
      </div>

      <div v-else-if="profile">
        <Transition name="fade" mode="out-in" appear>
          <component :is="dashboardComponent" v-if="dashboardComponent" />
          <div v-else class="text-center pt-24 text-yellow-400">
            <p class="text-xl font-semibold">Configuration Error</p>
            <p>Your role of "{{ profile.role }}" does not have a dashboard assigned.</p>
          </div>
        </Transition>
      </div>
    </main>
  </div>
</template>

<style scoped>
/* Fade Transition for dashboard content */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>