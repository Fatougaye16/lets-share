<template>
  <div class="max-w-sm p-6 bg-white border border-gray-200 rounded-lg shadow">

    <div class="h-28 w-full overflow-hidden rounded">
      <img :src="previewSrc" alt="document preview" class="w-full h-full object-cover" />
    </div>

    <div class=" border border-dashed border-amber-950 p-5 mt-6 rounded-lg">
      <h2 class=" font-semibold"> File Name: {{ file.fileName }}</h2>
      <p>Type: {{ file.fileType }}</p>
      <p>Size: {{ (file.fileSizeInBytes / 1024).toFixed(2) }} KB</p>
      <p>Date uploaded: {{ new Date(file.dateUploaded).toLocaleDateString() }}</p>
    </div>
    <div class="flex justify-end gap-6 mt-4">
      <div>
        <i class="bi bi-download text-2xl"></i>
      </div>
      <div>
        <i class="bi bi-trash3-fill text-red-700 text-2xl"></i>
      </div>
    </div>
  </div>
</template>

  <script setup>
import { defineProps, ref, onMounted, watch, computed } from 'vue';
import instance from '../api/agent.ts';
import doc from '../assets/images/doc.webp';
import { useUserStore } from '../stores/user'
const props = defineProps({ file: Object })

const userStore = useUserStore()
const token = computed(() => userStore.token)

const previewSrc = ref(doc)

const loadPreview = async () => {
  const ft = props.file?.fileType || ''
  if (ft.toLowerCase().startsWith('image') && props.file?.id) {
    try {
      const headers = token.value ? { Authorization: `Bearer ${token.value}` } : {}
      const response = await instance.get(`/download/${props.file.id}`, { responseType: 'blob', headers })
      const url = window.URL.createObjectURL(response.data)
      previewSrc.value = url
    } catch (err) {
      previewSrc.value = doc
      console.error('Preview fetch failed', err)
    }
  } else {
    previewSrc.value = doc
  }
}

onMounted(loadPreview)
watch(() => props.file?.id, loadPreview)
watch(token, loadPreview)

</script>

<style scoped>
.card {
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  overflow: hidden;
}

.card-body {
  padding: 1rem;
}

.card-title {
  font-size: 1.25rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}
</style>