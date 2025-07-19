<template>
  <div class="min-h-screen bg-gray-950 text-white p-6">
    <div class="flex justify-between items-center gap-4 mb-6">
      <div class="flex-grow max-w-sm">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="🔍 Search by title..."
          class="w-full px-3 py-1.5 bg-gray-800 border border-gray-700 rounded-md focus:outline-none focus:ring-2 focus:ring-[#00DC82]"
        />
      </div>

      <div class="flex gap-2">
        <button
          v-for="option in filterOptions"
          :key="option"
          @click="selectedStatus = option"
          class="px-4 py-1.5 text-sm rounded-md transition-colors"
          :class="{
            'bg-[#00DC82] text-gray-900 font-semibold': selectedStatus === option,
            'bg-gray-800 hover:bg-gray-700': selectedStatus !== option
          }"
        >
          {{ option }}
        </button>
      </div>
    </div>

    <div v-if="loading" class="flex justify-center items-center py-20">
      <svg class="animate-spin h-10 w-10 text-[#00DC82]" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
    </div>
    <div v-else-if="!visibleBugs.length" class="text-center py-20 border-2 border-dashed border-gray-800 rounded-lg">
      <h3 class="text-xl font-semibold text-white">No Bugs Found</h3>
      <p class="text-gray-400 mt-1">Try adjusting your search or filter criteria.</p>
    </div>

    <TransitionGroup v-else tag="div" name="list" class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3">
      <div
        v-for="bug in visibleBugs"
        :key="bug.id"
        class="bg-gray-900 rounded-xl shadow-lg p-5 flex flex-col justify-between border border-gray-800 hover:border-gray-700 transition-colors"
      >
        <div>
          <div class="flex justify-between items-start">
            <h2 class="text-xl font-bold mb-2 pr-2">{{ bug.title }}</h2>
            <span
              class="ml-2 px-2 py-0.5 rounded-full text-xs font-semibold shrink-0"
              :class="{
                'bg-red-600 text-white': bug.priority === 'High',
                'bg-yellow-400 text-gray-900': bug.priority === 'Medium',
                'bg-[#00DC82] text-gray-900': bug.priority === 'Low'
              }"
            >
              {{ bug.priority }}
            </span>
          </div>
          <p class="text-xs text-gray-500 mb-4">
            Reported on: {{ new Date(bug.created_at).toLocaleDateString() }}
          </p>

          <div class="mb-3">
            <label class="text-sm block mb-1 text-gray-400">Fix Note</label>
            <textarea
              v-model="bug.fix_note"
              class="w-full bg-gray-800 p-2 rounded border border-gray-700 text-white focus:outline-none focus:ring-2 focus:ring-[#00DC82]"
              placeholder="Add progress note or fix explanation..."
            ></textarea>
          </div>
        </div>

        <div class="flex flex-wrap justify-between items-center gap-3 mt-3">
          <span
            class="text-xs font-semibold px-3 py-1 rounded-full"
            :class="{
              'bg-gray-600 text-white': bug.status === 'Open',
              'bg-blue-600 text-white': bug.status === 'In Progress',
              'bg-[#00DC82] text-gray-900': bug.status === 'Fixed'
            }"
          >
            {{ bug.status }}
          </span>

          <div class="flex gap-2">
            <button
              v-if="bug.status === 'Open'"
              @click="startFixing(bug)"
              :disabled="loading"
              class="px-3 py-1 text-sm font-semibold rounded bg-[#00DC82] text-gray-900 hover:bg-opacity-80 transition-all disabled:opacity-50"
            >
              Start Fixing
            </button>
            <template v-else-if="bug.status === 'In Progress' && bug.assigned_to === user?.id">
              <button
                @click="markAsFixed(bug)"
                :disabled="loading"
                class="px-3 py-1 text-sm font-semibold rounded bg-[#00DC82] text-gray-900 hover:bg-opacity-80 transition-all disabled:opacity-50"
              >
                ✅ Complete
              </button>
              <button
                @click="cancelFixing(bug)"
                :disabled="loading"
                class="px-3 py-1 text-sm rounded bg-gray-600 hover:bg-gray-500 text-white transition-all disabled:opacity-50"
              >
                Cancel
              </button>
            </template>
          </div>
        </div>
      </div>
    </TransitionGroup>
  </div>
</template>

<script setup>
// The <script setup> block remains the same as the previous version.
// No logic changes are needed for the color scheme update.
import { ref, computed, onMounted } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'

const supabase = useSupabaseClient()
const user = useSupabaseUser()

const bugs = ref([])
const loading = ref(true) // Start as true for initial load

// State for search and filters
const searchQuery = ref('')
const selectedStatus = ref('All')
const filterOptions = ['All', 'Open', 'In Progress', 'Fixed']

/**
 * Updates a bug record in the local state array.
 * @param {object} updatedBug - The updated bug object returned from Supabase.
 */
const updateLocalBugState = (updatedBug) => {
  if (!updatedBug) return
  const index = bugs.value.findIndex(b => b.id === updatedBug.id)
  if (index !== -1) {
    bugs.value[index] = updatedBug
  }
}

const fetchBugs = async () => {
  loading.value = true
  const { data, error } = await supabase
    .from('bug_reports')
    .select('*')
    .order('created_at', { ascending: false })

  if (error) {
    console.error('Error fetching bugs:', error)
  } else {
    bugs.value = data
  }
  loading.value = false
}

const startFixing = async (bug) => {
  loading.value = true
  const { data: updatedBug, error } = await supabase
    .from('bug_reports')
    .update({ status: 'In Progress', assigned_to: user.value.id })
    .eq('id', bug.id)
    .select()
    .single()

  if (error) {
    console.error('Error starting fix:', error)
  } else {
    updateLocalBugState(updatedBug)
  }
  loading.value = false
}

const markAsFixed = async (bug) => {
  loading.value = true
  const { data: updatedBug, error } = await supabase
    .from('bug_reports')
    .update({ status: 'Fixed', fix_note: bug.fix_note })
    .eq('id', bug.id)
    .select()
    .single()

  if (error) {
    console.error('Error marking as fixed:', error)
  } else {
    updateLocalBugState(updatedBug)
  }
  loading.value = false
}

const cancelFixing = async (bug) => {
  loading.value = true
  const { data: updatedBug, error } = await supabase
    .from('bug_reports')
    .update({ status: 'Open', assigned_to: null, fix_note: '' })
    .eq('id', bug.id)
    .select()
    .single()

  if (error) {
    console.error('Error cancelling fix:', error)
  } else {
    updateLocalBugState(updatedBug)
  }
  loading.value = false
}

// Computed property now includes search and filtering
const visibleBugs = computed(() => {
  let filteredBugs

  // 1. First, filter by the selected status
  switch (selectedStatus.value) {
    case 'Open':
      filteredBugs = bugs.value.filter(b => b.status === 'Open')
      break
    case 'In Progress':
      filteredBugs = bugs.value.filter(b => b.status === 'In Progress' && b.assigned_to === user.value?.id)
      break
    case 'Fixed':
      filteredBugs = bugs.value.filter(b => b.status === 'Fixed')
      break
    case 'All':
    default:
      filteredBugs = bugs.value.filter(
        b => b.status === 'Open' || (b.status === 'In Progress' && b.assigned_to === user.value?.id)
      )
      break
  }

  // 2. Then, filter the result by the search query
  if (!searchQuery.value) {
    return filteredBugs
  }

  const lowerCaseQuery = searchQuery.value.toLowerCase()
  return filteredBugs.filter(bug =>
    bug.title.toLowerCase().includes(lowerCaseQuery)
  )
})

onMounted(() => {
  if (user.value) {
    fetchBugs()
  } else {
    loading.value = false // If no user, don't show loading forever
  }
})
</script>

<style scoped>
textarea {
  resize: vertical;
  min-height: 60px;
}

/* Transition for the list */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>