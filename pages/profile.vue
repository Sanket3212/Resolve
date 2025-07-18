<script setup>
import { ref, onMounted } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'
import { useRouter } from 'vue-router'

// Import role-based dashboard components
import AdminDashboard from '@/components/AdminDashboard.vue'
import TesterDashboard from '@/components/TesterDashboard.vue'
import DeveloperDashboard from '@/components/DeveloperDashboard.vue'

const supabase = useSupabaseClient()
const user = useSupabaseUser()
const router = useRouter()

const loading = ref(true)
const role = ref(null)

onMounted(async () => {
  if (!user.value) {
    router.push('/login')
    return
  }

  const { data, error } = await supabase
    .from('profiles')
    .select('role')
    .eq('id', user.value.id)
    .single()

  if (error) {
    console.error('Error fetching role:', error.message)
  } else {
    role.value = data.role
  }

  loading.value = false
})

const handleLogout = async () => {
  await supabase.auth.signOut()
  router.push('/login')
}
</script>
<template>
  <div class="min-h-screen bg-gray-900 text-white p-6">
    <div class="max-w-3xl mx-auto bg-gray-800 p-6 rounded-xl shadow-md">
      <h1 class="text-2xl font-bold mb-4">👤 Profile Page</h1>

      <p class="mb-2">Email: {{ user?.email }}</p>
      <p class="mb-4">Role: <strong>{{ role || 'loading...' }}</strong></p>

      <div v-if="loading">
        Loading dashboard...
      </div>

      <div v-else>
        <AdminDashboard v-if="role === 'admin'" />
        <TesterDashboard v-else-if="role === 'tester'" />
        <DeveloperDashboard v-else-if="role === 'developer'" />
        <p v-else class="text-red-400">❌ Unknown role</p>
      </div>

      <button
        @click="handleLogout"
        class="mt-6 bg-red-600 hover:bg-red-700 text-white py-2 px-4 rounded"
      >
        Logout
      </button>
    </div>
  </div>
</template>
