<template>
  <div class="p-4 sm:p-6 space-y-6">
    <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
      <h1 class="text-2xl font-bold text-white">Admin Dashboard</h1>
      <button @click="showRegisterModal = true" class="flex items-center justify-center gap-2 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg font-semibold transition-transform hover:scale-105">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" /></svg>
        <span>Register User</span>
      </button>
    </div>

    <div v-if="loading" class="space-y-6">
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
        <div v-for="i in 4" :key="i" class="h-28 bg-zinc-800 rounded-xl animate-pulse"></div>
      </div>
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="lg:col-span-2 h-96 bg-zinc-800 rounded-xl animate-pulse"></div>
        <div class="space-y-6">
            <div class="h-80 bg-zinc-800 rounded-xl animate-pulse"></div>
            <div class="h-80 bg-zinc-800 rounded-xl animate-pulse"></div>
        </div>
      </div>
    </div>

    <div v-else class="space-y-6">
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 animate-fade-in-down" style="animation-delay: 100ms;">
        <Card title="Total Users" :value="users.length" icon="users" />
        <Card title="Bug Reports" :value="bugReports.length" icon="bugs" />
        <Card title="Active Testers" :value="testerCount" icon="testers" />
        <Card title="Active Developers" :value="developerCount" icon="devs" />
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 animate-fade-in-down" style="animation-delay: 200ms;">
        <div class="lg:col-span-2 bg-zinc-900 p-4 sm:p-6 rounded-2xl shadow-lg">
          <h2 class="text-xl font-semibold text-white mb-4">User Management</h2>
          <div class="overflow-x-auto">
            <table class="w-full text-left text-white min-w-[600px]">
              <thead>
                <tr class="text-zinc-400 border-b border-zinc-700">
                  <th class="p-3 font-semibold">Name</th>
                  <th class="p-3 font-semibold">Role</th>
                  <th class="p-3 font-semibold">Email</th>
                  <th class="p-3 font-semibold text-right">Actions</th>
                </tr>
              </thead>
              <TransitionGroup name="list-item" tag="tbody">
                <tr v-if="users.length === 0" key="empty-row">
                  <td colspan="4" class="text-center py-10 text-zinc-500">No users found.</td>
                </tr>
                <tr v-for="user in users" :key="user.id" class="border-b border-zinc-800 hover:bg-zinc-800/50 transition-colors">
                  <td class="p-3 font-medium">{{ user.name || 'Unnamed' }}</td>
                  <td class="p-3">
                    <span :class="roleClass(user.role)" class="px-2 py-1 text-xs font-bold rounded-full capitalize">{{ user.role }}</span>
                  </td>
                  <td class="p-3 text-zinc-400">{{ user.email }}</td>
                  <td class="p-3 text-right">
                    <button class="text-sm text-red-500 hover:text-red-400 font-semibold" @click="deleteUser(user.id)">Delete</button>
                  </td>
                </tr>
              </TransitionGroup>
            </table>
          </div>
        </div>

        <div class="space-y-6">
            
            <div class="bg-zinc-900 p-4 sm:p-6 rounded-2xl shadow-lg">
                <h2 class="text-xl font-semibold text-white mb-4">Bugs by Status</h2>
                <div class="h-64">
                    <BarGraph :chart-data="bugsByStatusData" />
                </div>
            </div>
            <div class="bg-zinc-900 p-4 sm:p-6 rounded-2xl shadow-lg">
              <h2 class="text-xl font-semibold text-white mb-4">Bug Report Overview</h2>
              <ul class="space-y-3 max-h-[400px] overflow-y-auto pr-2">
                <li v-if="bugReports.length === 0" class="text-center py-10 text-zinc-500">No bug reports yet.</li>
                <li v-for="bug in bugReports" :key="bug.id" class="p-4 bg-zinc-800 rounded-lg space-y-3">
                  <div>
                    <p class="font-semibold text-white truncate">{{ bug.title }}</p>
                    <div class="flex items-center justify-between mt-2 text-xs">
                       <span class="px-2 py-0.5 font-bold rounded-full" :class="statusClass(bug.status)">{{ bug.status }}</span>
                       <span class="px-2 py-0.5 font-bold rounded-full" :class="priorityClass(bug.priority)">{{ bug.priority }}</span>
                    </div>
                  </div>
                  <div class="border-t border-zinc-700 pt-3">
                    <p class="text-xs text-zinc-400 mb-2">
                      Assigned to: 
                      <strong class="text-white">{{ bug.profiles?.name || 'Unassigned' }}</strong>
                    </p>
                    <div v-if="bug.status === 'Open'" class="flex gap-2">
                      <select v-model="bug.selectedDeveloper" class="w-full text-sm px-3 py-1.5 rounded bg-zinc-700 text-white border border-zinc-600 focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option :value="null" disabled>Select Developer</option>
                        <option v-for="dev in developers" :key="dev.id" :value="dev.id">
                          {{ dev.name }}
                        </option>
                      </select>
                      <button @click="assignBug(bug.id, bug.selectedDeveloper)" :disabled="!bug.selectedDeveloper" class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1 text-sm rounded-lg font-semibold disabled:bg-zinc-500 disabled:cursor-not-allowed shrink-0">
                        Assign
                      </button>
                    </div>
                  </div>
                </li>
              </ul>
            </div>
        </div>
      </div>
    </div>

    <Transition name="modal">
      <div v-if="showRegisterModal" class="fixed inset-0 z-50 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-zinc-800 p-6 rounded-xl shadow-lg w-full max-w-md border border-zinc-700">
          <h2 class="text-xl font-bold mb-4 text-white">Register New User</h2>
          <form @submit.prevent="registerUser" class="space-y-4">
            <div>
              <label class="text-sm block mb-1 text-white">Name</label>
              <input v-model="newUser.name" type="text" class="w-full px-3 py-2 rounded bg-gray-700 text-white" required />
            </div>
            <div>
              <label class="text-sm block mb-1 text-white">Email</label>
              <input v-model="newUser.email" type="email" class="w-full px-3 py-2 rounded bg-gray-700 text-white" required />
            </div>
            <div>
              <label class="text-sm block mb-1 text-white">Password</label>
              <input v-model="newUser.password" type="password" class="w-full px-3 py-2 rounded bg-gray-700 text-white" required />
            </div>
            <div>
              <label class="text-sm block mb-1 text-white">Role</label>
              <select v-model="newUser.role" class="w-full px-3 py-2 rounded bg-gray-700 text-white">
                <option value="admin">Admin</option>
                <option value="tester">Tester</option>
                <option value="developer">Developer</option>
              </select>
            </div>
            <div v-if="registerError" class="text-red-400 text-sm p-3 bg-red-500/10 rounded-lg">{{ registerError }}</div>
            <div v-if="registerSuccess" class="text-green-400 text-sm p-3 bg-green-500/10 rounded-lg">{{ registerSuccess }}</div>
            <div class="flex justify-end gap-2 pt-2">
              <button type="button" @click="closeModal" class="px-4 py-2 text-sm bg-gray-600 rounded font-semibold">Cancel</button>
              <button type="submit" :disabled="isRegistering" class="bg-blue-600 hover:bg-blue-700 px-4 py-2 rounded text-white font-semibold flex items-center justify-center w-28">
                <span v-if="!isRegistering">Register</span>
                <svg v-else class="animate-spin h-5 w-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
              </button>
            </div>
          </form>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import { useSupabaseClient } from '#imports';
import Card from '@/components/Card.vue';
import PieChart from '@/components/PieChart.vue';
import BarGraph from '@/components/BarGraph.vue';

const supabase = useSupabaseClient();
const loading = ref(true);
const isRegistering = ref(false);

const users = ref([]);
const bugReports = ref([]);
const showRegisterModal = ref(false);
const registerError = ref('');
const registerSuccess = ref('');
const newUser = ref({ name: '', email: '', password: '', role: 'tester' });

// Data Fetching
const fetchData = async () => {
  loading.value = true;
  try {
    await Promise.all([fetchUsers(), fetchBugReports()]);
  } catch (e) {
    console.error("Dashboard failed to load data", e);
  } finally {
    loading.value = false;
  }
};

const fetchUsers = async () => {
  const { data, error } = await supabase.from('profiles').select('id, name, email, role');
  if (error) console.error('Error fetching users:', error.message);
  else users.value = data;
};

const fetchBugReports = async () => {
  const { data, error } = await supabase
    .from('bug_reports')
    .select('id, title, status, priority, profiles ( name )')
    .order('created_at', { ascending: false });

  if (error) {
    console.error('Error fetching bugs:', error.message);
  } else {
    bugReports.value = data.map(bug => ({ ...bug, selectedDeveloper: null }));
  }
};

// Computed properties for cards and charts
const developers = computed(() => users.value.filter(u => u.role === 'developer'));
const testerCount = computed(() => users.value.filter(u => u.role === 'tester').length);
const developerCount = computed(() => developers.value.length);
const adminCount = computed(() => users.value.filter(u => u.role === 'admin').length);

const userRolesData = computed(() => ({
  labels: ['Developers', 'Testers', 'Admins'],
  datasets: [{
    backgroundColor: ['#3b82f6', '#22c55e', '#ef4444'],
    data: [developerCount.value, testerCount.value, adminCount.value]
  }]
}));

const bugsByStatusData = computed(() => {
    const statusCounts = bugReports.value.reduce((acc, bug) => {
        acc[bug.status] = (acc[bug.status] || 0) + 1;
        return acc;
    }, {});
    return {
        labels: ['Open', 'In Progress', 'Fixed'],
        datasets: [{
            label: 'Bug Reports',
            backgroundColor: ['#71717a', '#3b82f6', '#22c55e'],
            data: [
                statusCounts['Open'] || 0,
                statusCounts['In Progress'] || 0,
                statusCounts['Fixed'] || 0
            ]
        }]
    };
});

// User Actions
const deleteUser = async (userId) => {
  if (!confirm('Are you sure you want to delete this user? This action cannot be undone.')) return;
  const { error } = await supabase.from('profiles').delete().eq('id', userId);
  if (error) {
    alert(`Failed to delete user: ${error.message}`);
  } else {
    await fetchUsers();
  }
};

const registerUser = async () => {
  isRegistering.value = true;
  registerError.value = '';
  registerSuccess.value = '';

  try {
    const { data: signUpData, error: signUpError } = await supabase.auth.signUp({
      email: newUser.value.email,
      password: newUser.value.password,
    });

    if (signUpError) throw signUpError;

    const userId = signUpData.user?.id;
    if (!userId) throw new Error("User created, but ID was not returned.");

    const { error: profileError } = await supabase.from('profiles').upsert({
      id: userId,
      name: newUser.value.name,
      email: newUser.value.email,
      role: newUser.value.role,
      updated_at: new Date(),
    });

    if (profileError) throw profileError;

    registerSuccess.value = 'User registered successfully!';
    await fetchUsers();
    setTimeout(closeModal, 2000);

  } catch (error) {
    registerError.value = error.message;
  } finally {
    isRegistering.value = false;
  }
};

const closeModal = () => {
  showRegisterModal.value = false;
  registerError.value = '';
  registerSuccess.value = '';
  newUser.value = { name: '', email: '', password: '', role: 'tester' };
};

// Styling Helpers
const roleClass = (role) => ({
  'bg-rose-500/20 text-rose-400': role === 'admin',
  'bg-sky-500/20 text-sky-400': role === 'developer',
  'bg-emerald-500/20 text-emerald-400': role === 'tester',
});

const statusClass = (status) => ({
  'bg-zinc-600 text-zinc-100': status === 'Open',
  'bg-sky-500 text-sky-100': status === 'In Progress',
  'bg-emerald-500 text-emerald-100': status === 'Fixed',
});

const priorityClass = (priority) => ({
    'bg-red-500 text-white': priority === 'High',
    'bg-yellow-500 text-black': priority === 'Medium',
    'bg-green-500 text-white': priority === 'Low'
});

onMounted(fetchData);
</script>

<style scoped>
/* Modal Transition */
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}
.modal-enter-from,
.modal-leave-to {
  opacity: 0;
  transform: scale(0.95) translateY(-20px);
}

/* List Item (Table Row) Transition */
.list-item-enter-active,
.list-item-leave-active {
  transition: all 0.4s ease;
}
.list-item-enter-from,
.list-item-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.list-item-leave-active {
  position: absolute;
}

/* Entrance Animation */
@keyframes fade-in-down {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fade-in-down {
  animation: fade-in-down 0.5s ease-out forwards;
  opacity: 0;
}
</style>