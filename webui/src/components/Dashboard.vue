<template>
  <div class="pr-6 py-6 mr-12 min-h-screen md:min-h-0">
    <div class="mb-6">
      <div class="flex items-start justify-between gap-6">
        <div>
          <h1 class="text-3xl text-amber-950 font-bold">Dashboard</h1>
          <p class="text-sm text-stone-600 mt-1">Welcome back, <span class="font-medium">{{ user?.userName || 'User' }}</span></p>
        </div>

        <div class="flex items-center gap-4">
          <label class="input input-bordered flex items-center gap-2 w-64">
            <input type="text" class="grow" placeholder="Search files" v-model="searchTerm" aria-label="Search files" />
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="h-4 w-4 opacity-70">
              <path fill-rule="evenodd"
                d="M9.965 11.026a5 5 0 1 1 1.06-1.06l2.755 2.754a.75.75 0 1 1-1.06 1.06l-2.755-2.754ZM10.5 7a3.5 3.5 0 1 1-7 0 3.5 3.5 0 0 1 7 0Z"
                clip-rule="evenodd" />
            </svg>
          </label>

          <button @click="goToMyDocs" class="btn bg-amber-950 text-white rounded-lg px-4">My Documents</button>
        </div>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 mt-6">
        <div class="p-4 bg-white rounded-lg shadow-sm border border-stone-200">
          <div class="text-sm text-stone-500">Total files</div>
          <div class="text-2xl font-semibold mt-1">{{ totalFiles }}</div>
        </div>
        <div class="p-4 bg-white rounded-lg shadow-sm border border-stone-200">
          <div class="text-sm text-stone-500">Total size</div>
          <div class="text-2xl font-semibold mt-1">{{ totalSizeKB }} KB</div>
        </div>
        <div class="p-4 bg-white rounded-lg shadow-sm border border-stone-200">
          <div class="text-sm text-stone-500">Recent upload</div>
          <div class="text-2xl font-semibold mt-1">{{ recentUploadName }}</div>
        </div>
      </div>
    </div>

    <div class="overflow-y-auto">
      <div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-6 mt-6">
        <div v-if="filteredFiles.length === 0" class="col-span-full">
          <div class="p-12 text-center border rounded-lg bg-white">
            <img src="../assets/images/education.gif" alt="empty" class="mx-auto mb-4 w-40 h-40" />
            <h3 class="text-xl font-semibold mb-2">No files yet</h3>
            <p class="text-sm text-stone-600 mb-4">Upload your first document to get started.</p>
            <button class="btn bg-amber-950 text-white" @click="goToMyDocs">Upload a file</button>
          </div>
        </div>

        <div v-for="file in filteredFiles" :key="file.id">
          <Card :file="file" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Card from './DashboardCard.vue'
import instance from '../api/agent.ts'
import { useUserStore } from '../stores/user'
import { computed, ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const userStore = useUserStore()
const user = computed(() => userStore.user)
const files = ref([])
const searchTerm = ref('')
const router = useRouter()

const getFiles = async () => {
  try {
    const response = await instance.get('/all-files');
    files.value = response.data || [];
  } catch (error) {
    console.error(error);
  }
}

onMounted(() => { getFiles(); })

const filteredFiles = computed(() => {
  if (!files.value) return []
  if (!searchTerm.value) return files.value
  return files.value.filter(f => f.fileName.toLowerCase().includes(searchTerm.value.toLowerCase()))
})

const totalFiles = computed(() => files.value?.length || 0)
const totalSizeKB = computed(() => {
  const total = (files.value || []).reduce((acc, f) => acc + (f.fileSizeInBytes || 0), 0)
  return (total / 1024).toFixed(0)
})

const recentUploadName = computed(() => {
  if (!files.value || files.value.length === 0) return '—'
  return files.value[0].fileName || '—'
})

const goToMyDocs = () => router.push('/my-documents')
</script>
