<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import { useSupabaseClient, useSupabaseUser } from '#imports'

const supabase = useSupabaseClient()
const user = useSupabaseUser()

const title = ref('')
const description = ref('')
const priority = ref('Low')
const imageFile = ref(null)
const bugs = ref([])
const loading = ref(true)
const isSubmitting = ref(false)

const editingBug = ref(null)
const editedTitle = ref('')
const editedDescription = ref('')
const editedPriority = ref('Low')
const isSaving = ref(false)

const emit = defineEmits(['bugReportsVisible'])

const fetchBugs = async () => {
  loading.value = true
  const { data, error } = await supabase
    .from('bug_reports')
    .select('*')
    .eq('created_by', user.value.id)
    .order('created_at', { ascending: false })

  if (!error) bugs.value = data
  loading.value = false
}

const handleFileSelect = (e) => {
  imageFile.value = e.target.files[0]
}

const filePreviewUrl = computed(() => {
    if (imageFile.value) {
        return URL.createObjectURL(imageFile.value)
    }
    return null
})

const handleBugSubmit = async () => {
  isSubmitting.value = true
  let imageUrl = null

  if (imageFile.value) {
    const fileExt = imageFile.value.name.split('.').pop()
    const filePath = `bug-images/${Date.now()}.${fileExt}`

    const { error: uploadError } = await supabase.storage
      .from('bug_images')
      .upload(filePath, imageFile.value)

    if (!uploadError) {
      const { data: publicUrl } = supabase.storage
        .from('bug_images')
        .getPublicUrl(filePath)
      imageUrl = publicUrl.publicUrl
    }
  }

  const { error } = await supabase.from('bug_reports').insert([{
    title: title.value,
    description: description.value,
    priority: priority.value,
    image_url: imageUrl,
    created_by: user.value.id,
    status: 'Open' // Explicitly set status
  }])

  if (!error) {
    title.value = ''
    description.value = ''
    priority.value = 'Low'
    imageFile.value = null
    // Reset file input visually
    const fileInput = document.getElementById('file-upload')
    if (fileInput) fileInput.value = ''
    fetchBugs()
  }
  isSubmitting.value = false
}

const startEditing = (bug) => {
  editingBug.value = bug
  editedTitle.value = bug.title
  editedDescription.value = bug.description
  editedPriority.value = bug.priority
}

const saveBugEdit = async () => {
  if (!editingBug.value) return
  isSaving.value = true
  const { error } = await supabase
    .from('bug_reports')
    .update({
      title: editedTitle.value,
      description: editedDescription.value,
      priority: editedPriority.value
    })
    .eq('id', editingBug.value.id)

  if (!error) {
    editingBug.value = null
    fetchBugs()
  }
  isSaving.value = false
}

onMounted(() => {
  if (user.value) fetchBugs()
})

// INTERSECTION OBSERVER
const bugHeaderRef = ref(null)
let observer = null

onMounted(() => {
  observer = new IntersectionObserver(
    ([entry]) => {
      emit('bugReportsVisible', entry.isIntersecting)
    },
    { threshold: 0.1 }
  )

  if (bugHeaderRef.value) {
    observer.observe(bugHeaderRef.value)
  }
})

onUnmounted(() => {
  if (observer && bugHeaderRef.value) observer.unobserve(bugHeaderRef.value)
})
</script>

<template>
  <div class="flex flex-col lg:flex-row h-screen bg-[#121212] text-white">
    <div class="w-full lg:w-1/3 lg:h-full p-6 bg-[#1E1E1E] overflow-y-auto shadow-xl border-r border-zinc-800">
      <h2 class="text-2xl font-bold mb-6">🐞 Report a Bug</h2>

      <form @submit.prevent="handleBugSubmit" class="space-y-5">
        <div>
          <label class="block text-sm font-medium mb-1 text-zinc-400">Title</label>
          <input
            v-model="title"
            class="w-full px-3 py-2 rounded-lg bg-[#272727] text-white border border-[#333] focus:outline-none focus:ring-2 focus:ring-[#00DC82]"
            type="text"
            required
            placeholder="e.g., Login button not working on mobile"
          />
        </div>

        <div>
          <label class="block text-sm font-medium mb-1 text-zinc-400">Description</label>
          <textarea
            v-model="description"
            class="w-full px-3 py-2 rounded-lg bg-[#272727] text-white border border-[#333] focus:outline-none focus:ring-2 focus:ring-[#00DC82] min-h-[100px]"
            required
            placeholder="Describe the issue in detail..."
          ></textarea>
        </div>

        <div>
          <label class="block text-sm font-medium mb-2 text-zinc-400">Priority</label>
          <div class="flex gap-2">
            <button type="button" @click="priority = 'Low'" :class="priority === 'Low' ? 'bg-green-500 text-white' : 'bg-[#272727] hover:bg-[#333]'" class="flex-1 py-2 rounded-lg text-sm font-semibold transition">Low</button>
            <button type="button" @click="priority = 'Medium'" :class="priority === 'Medium' ? 'bg-yellow-500 text-black' : 'bg-[#272727] hover:bg-[#333]'" class="flex-1 py-2 rounded-lg text-sm font-semibold transition">Medium</button>
            <button type="button" @click="priority = 'High'" :class="priority === 'High' ? 'bg-red-500 text-white' : 'bg-[#272727] hover:bg-[#333]'" class="flex-1 py-2 rounded-lg text-sm font-semibold transition">High</button>
          </div>
        </div>
        
        <div>
          <label class="block text-sm font-medium mb-2 text-zinc-400">Screenshot (optional)</label>
          <label for="file-upload" class="flex flex-col items-center justify-center w-full h-32 border-2 border-dashed border-[#333] rounded-lg cursor-pointer bg-[#272727] hover:bg-[#333]">
            <div class="flex flex-col items-center justify-center pt-5 pb-6">
              <svg class="w-8 h-8 mb-4 text-zinc-500" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 16"><path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 13h3a3 3 0 0 0 0-6h-.025A5.56 5.56 0 0 0 16 6.5 5.5 5.5 0 0 0 5.207 5.021C5.137 5.017 5.071 5 5 5a4 4 0 0 0 0 8h2.167M10 15V6m0 0L8 8m2-2 2 2"/></svg>
              <p v-if="!imageFile" class="mb-2 text-sm text-zinc-500"><span class="font-semibold">Click to upload</span> or drag and drop</p>
              <p v-else class="mb-2 text-sm text-green-400 font-semibold">{{ imageFile.name }}</p>
            </div>
            <input id="file-upload" type="file" class="hidden" @change="handleFileSelect" accept="image/*" />
          </label>
          <img v-if="filePreviewUrl" :src="filePreviewUrl" class="mt-4 rounded-lg max-h-40" alt="Image preview"/>
        </div>

        <button
          type="submit"
          :disabled="isSubmitting"
          class="w-full py-3 px-4 bg-[#00DC82] hover:bg-[#00b86b] text-black font-semibold rounded-lg transition flex items-center justify-center disabled:bg-zinc-500"
        >
            <svg v-if="isSubmitting" class="animate-spin -ml-1 mr-3 h-5 w-5 text-black" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
            <span>{{ isSubmitting ? 'Submitting...' : 'Submit Bug' }}</span>
        </button>
      </form>
    </div>

    <div class="w-full lg:w-2/3 h-full overflow-y-auto p-6">
      <h2 ref="bugHeaderRef" class="text-2xl font-bold mb-4 text-white sticky top-0 bg-[#121212]/80 backdrop-blur-sm z-10 py-4 text-center">
        🐛 Your Bug Reports
      </h2>

      <div v-if="loading" class="space-y-4">
        <div v-for="i in 3" :key="i" class="bg-zinc-800 p-4 rounded-xl shadow animate-pulse">
            <div class="h-6 bg-zinc-700 rounded w-3/4 mb-4"></div>
            <div class="h-4 bg-zinc-700 rounded w-full mb-2"></div>
            <div class="h-4 bg-zinc-700 rounded w-1/2"></div>
        </div>
      </div>
      
      <div v-else-if="!bugs.length" class="text-center py-20 border-2 border-dashed border-zinc-800 rounded-lg">
        <h3 class="text-xl font-semibold text-white">No Bugs Reported Yet</h3>
        <p class="text-zinc-400 mt-1">Use the form on the left to report your first bug.</p>
      </div>

      <TransitionGroup v-else name="list" tag="div" class="space-y-4">
        <div
          v-for="bug in bugs"
          :key="bug.id"
          class="bg-zinc-800 p-4 rounded-xl shadow border border-transparent hover:border-zinc-700 transition"
        >
            <div class="flex justify-between items-start">
                <h3 class="text-lg font-semibold text-white">{{ bug.title }}</h3>
                <button @click="startEditing(bug)" class="ml-4 text-sm text-blue-400 hover:underline whitespace-nowrap">Edit</button>
            </div>
            <div class="flex items-center gap-4 mt-1 text-xs text-zinc-400">
                <span>{{ new Date(bug.created_at).toLocaleDateString() }}</span>
                <span class="w-1 h-1 bg-zinc-600 rounded-full"></span>
                <span class="px-2 py-0.5 font-bold rounded-full" :class="{ 'bg-zinc-600 text-zinc-100': bug.status === 'Open', 'bg-sky-500 text-sky-100': bug.status === 'In Progress', 'bg-emerald-500 text-emerald-100': bug.status === 'Fixed' }">{{ bug.status }}</span>
                <span class="px-2 py-0.5 font-bold rounded-full" :class="{ 'bg-red-500 text-white': bug.priority === 'High', 'bg-yellow-500 text-black': bug.priority === 'Medium', 'bg-green-500 text-white': bug.priority === 'Low' }">{{ bug.priority }}</span>
            </div>
            <p v-if="bug.description" class="text-sm text-zinc-300 mt-3 border-l-2 border-zinc-700 pl-3">{{ bug.description }}</p>
            <img v-if="bug.image_url" :src="bug.image_url" class="mt-4 rounded-lg max-h-64 cursor-pointer" @click="() => window.open(bug.image_url, '_blank')" alt="Bug screenshot"/>
        </div>
      </TransitionGroup>
    </div>

    <Transition name="modal">
      <div v-if="editingBug" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50 p-4">
        <div class="bg-[#1E1E1E] p-6 rounded-xl w-full max-w-md border border-[#333]">
          <h3 class="text-lg font-bold mb-4 text-white">✏️ Edit Bug</h3>
          <div class="space-y-4">
            <div>
              <label class="text-sm block mb-1 text-white">Title</label>
              <input v-model="editedTitle" class="w-full px-3 py-2 bg-[#272727] text-white rounded-lg border border-[#333] focus:outline-none focus:ring-2 focus:ring-[#00DC82]" />
            </div>
            <div>
              <label class="text-sm block mb-1 text-white">Description</label>
              <textarea v-model="editedDescription" class="w-full px-3 py-2 bg-[#272727] text-white rounded-lg border border-[#333] focus:outline-none focus:ring-2 focus:ring-[#00DC82] min-h-[100px]"></textarea>
            </div>
            <div>
              <label class="text-sm block mb-1 text-white">Priority</label>
              <select v-model="editedPriority" class="w-full px-3 py-2 bg-[#272727] text-white rounded-lg border border-[#333] focus:outline-none focus:ring-2 focus:ring-[#00DC82]">
                <option>Low</option>
                <option>Medium</option>
                <option>High</option>
              </select>
            </div>
          </div>
          <div class="flex justify-end gap-2 mt-6">
            <button @click="editingBug = null" class="px-4 py-2 bg-[#333] text-white rounded-lg hover:bg-[#444] transition">Cancel</button>
            <button @click="saveBugEdit" :disabled="isSaving" class="px-4 py-2 bg-[#00DC82] text-black font-semibold rounded-lg hover:bg-[#00b86b] transition flex items-center justify-center w-24">
              <svg v-if="isSaving" class="animate-spin h-5 w-5 text-black" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
              <span v-else>Save</span>
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}
.modal-enter-from,
.modal-leave-to {
  opacity: 0;
  transform: scale(0.95) translateY(-10px);
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
</style>