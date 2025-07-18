<script setup>
import { ref, onMounted } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'
import { useRouter } from 'vue-router'

const supabase = useSupabaseClient()
const user = useSupabaseUser()
const router = useRouter()

const loading = ref(true)
const username = ref('')
const website = ref('')
const avatar_url = ref('')
const role = ref('') // optional for future use

onMounted(async () => {
  if (!user.value) return

  loading.value = true

  const { data, error } = await supabase
    .from('profiles')
    .select(`username, website, avatar_url, role`)
    .eq('id', user.value.id)
    .single()

  if (error) {
    console.error(error.message)
  } else if (data) {
    username.value = data.username
    website.value = data.website
    avatar_url.value = data.avatar_url
    role.value = data.role
  }

  loading.value = false
})

const updateProfile = async () => {
  try {
    loading.value = true

    const updates = {
      id: user.value.id,
      username: username.value,
      website: website.value,
      avatar_url: avatar_url.value,
      updated_at: new Date(),
    }

    const { error } = await supabase.from('profiles').upsert(updates)
    if (error) throw error

    alert('✅ Profile updated!')
  } catch (error) {
    alert('❌ ' + error.message)
  } finally {
    loading.value = false
  }
}

const signOut = async () => {
  try {
    loading.value = true
    const { error } = await supabase.auth.signOut()
    if (error) throw error
    router.push('/login')
  } catch (error) {
    alert('❌ ' + error.message)
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="bg-gray-900 text-white min-h-screen flex items-center justify-center p-6">
    <div class="bg-gray-800 w-full max-w-md p-6 rounded-xl shadow-lg">
      <h2 class="text-2xl font-semibold mb-4">Account Settings</h2>

      <form @submit.prevent="updateProfile" class="space-y-4">
        <div>
          <label class="text-sm block mb-1">Email</label>
          <input
            type="email"
            :value="user?.email"
            disabled
            class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
          />
        </div>

        <div>
          <label class="text-sm block mb-1">Username</label>
          <input
            type="text"
            v-model="username"
            class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
          />
        </div>

        <div>
          <label class="text-sm block mb-1">Website</label>
          <input
            type="url"
            v-model="website"
            class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
          />
        </div>

        <div>
          <label class="text-sm block mb-1">Avatar URL</label>
          <input
            type="url"
            v-model="avatar_url"
            class="w-full px-4 py-2 rounded bg-gray-700 border border-gray-600"
          />
        </div>

        <div v-if="role" class="text-sm text-gray-400">🔒 Role: {{ role }}</div>

        <div class="flex space-x-2 pt-4">
          <button
            type="submit"
            :disabled="loading"
            class="w-full bg-blue-600 hover:bg-blue-700 py-2 rounded text-white font-medium"
          >
            {{ loading ? 'Updating...' : 'Update Profile' }}
          </button>

          <button
            type="button"
            :disabled="loading"
            @click="signOut"
            class="w-full bg-red-600 hover:bg-red-700 py-2 rounded text-white font-medium"
          >
            Sign Out
          </button>
        </div>
      </form>
    </div>
  </div>
</template>
