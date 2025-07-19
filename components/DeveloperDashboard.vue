<template>
  <div class="min-h-screen bg-[#121212] text-white p-6">
    <div class="flex justify-between items-center gap-4 mb-6">
      <div class="flex-grow max-w-sm">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="🔍 Search by title..."
          class="w-full px-3 py-1.5 bg-[#272727] border border-[#333] rounded-md focus:outline-none focus:ring-2 focus:ring-blue-600"
        />
      </div>

      <div class="flex gap-2">
        <button
          v-for="option in filterOptions"
          :key="option"
          @click="selectedStatus = option"
          class="px-4 py-1.5 text-sm rounded-md transition-colors"
          :class="{
            'bg-blue-600 text-white': selectedStatus === option,
            'bg-[#272727] hover:bg-[#333]': selectedStatus !== option
          }"
        >
          {{ option }}
        </button>
      </div>
    </div>

    <div v-if="loading" class="flex justify-center items-center py-20">
        <svg class="animate-spin h-10 w-10 text-blue-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
        </svg>
    </div>

    <div v-else-if="!visibleBugs.length" class="text-center py-20 border-2 border-dashed border-[#272727] rounded-lg">
      <h3 class="text-xl font-semibold text-white">No Bugs Found</h3>
      <p class="text-gray-400 mt-1">Try adjusting your search or filter criteria.</p>
    </div>

    <TransitionGroup
      v-else
      tag="div"
      name="list"
      class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3"
    >
      <div
        v-for="bug in visibleBugs"
        :key="bug.id"
        class="bg-[#1E1E1E] rounded-xl shadow-lg p-5 flex flex-col justify-between transition-all duration-300 hover:scale-105 hover:shadow-blue-500/10"
      >
        <div>
          <h2 class="text-xl font-bold mb-2">{{ bug.title }}</h2>

          <p class="text-sm text-gray-400 mb-2">
            Priority:
            <span
              class="ml-2 px-2 py-0.5 rounded-full text-xs font-semibold"
              :class="{
                'bg-red-600 text-white': bug.priority === 'High',
                'bg-yellow-400 text-black': bug.priority === 'Medium',
                'bg-green-500 text-white': bug.priority === 'Low'
              }"
            >
              {{ bug.priority }}
            </span>
          </p>

          <div class="mb-3">
            <label class="text-sm block mb-1">Fix Note</label>
            <textarea
              v-model="bug.fix_note"
              class="w-full bg-[#272727] p-2 rounded border border-[#333] text-white"
              placeholder="Add progress note or fix explanation..."
            ></textarea>
          </div>
        </div>

        <div class="flex flex-wrap justify-between items-center gap-3 mt-3">
          <span
            class="text-xs font-semibold px-3 py-1 rounded-full"
            :class="{
              'bg-gray-500 text-white': bug.status === 'Open',
              'bg-blue-600 text-white': bug.status === 'In Progress',
              'bg-green-600 text-white': bug.status === 'Fixed'
            }"
          >
            {{ bug.status }}
          </span>

          <div class="flex gap-2">
            <button
              v-if="bug.status === 'Open'"
              @click="startFixing(bug)"
              :disabled="loading"
              class="px-3 py-1 text-sm rounded bg-blue-500 hover:bg-blue-600 text-white disabled:opacity-50"
            >
              Start Fixing
            </button>

            <template v-else-if="bug.status === 'In Progress' && bug.assigned_to === user?.id">
              <button
                @click="markAsFixed(bug)"
                :disabled="loading"
                class="px-3 py-1 text-sm rounded bg-green-600 hover:bg-green-700 text-white disabled:opacity-50"
              >
                ✅ Complete
              </button>
              <button
                @click="cancelFixing(bug)"
                :disabled="loading"
                class="px-3 py-1 text-sm rounded bg-gray-500 hover:bg-gray-600 text-white disabled:opacity-50"
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
import { ref, computed, onMounted } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'

const supabase = useSupabaseClient()
const user = useSupabaseUser()

const bugs = ref([])
const loading = ref(true) // Start loading as true for initial fetch

// State for search and filters
const searchQuery = ref('')
const selectedStatus = ref('All')
const filterOptions = ['All', 'Open', 'In Progress', 'Fixed']

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
}

const markAsFixed = async (bug) => {
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
}

const cancelFixing = async (bug) => {
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
}

const visibleBugs = computed(() => {
  let filteredBugs

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
    loading.value = false
  }
})
</script>

<style scoped>
textarea {
  resize: vertical;
  min-height: 60px;
}

/* Animation for the list */
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

<style>
html {
  scroll-behavior: smooth;
}
</style>