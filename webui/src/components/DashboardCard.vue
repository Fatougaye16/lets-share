<template>
    <div role="group" tabindex="0" class="max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow transition-transform hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-amber-950 focus:ring-offset-2 h-full flex flex-col justify-between">
        <div class="h-28 w-full overflow-hidden rounded">
          <img :src="previewSrc" alt="document preview" class="w-full h-full object-cover"/>
        </div>

        <div class="border border-dashed border-amber-950 p-3 mt-4 rounded-lg flex-1">
            <h2 class="font-semibold text-sm truncate">{{ file.fileName }}</h2>
            <div class="mt-2 text-xs text-stone-600">
              <div>Type: {{ file.fileType }}</div>
              <div>Size: {{ (file.fileSizeInBytes / 1024).toFixed(2) }} KB</div>
            </div>
        </div>

        <div class="flex justify-end gap-3 mt-3">
            <button @click="downloadFile" aria-label="Download {{ file.fileName }}" class="p-2 rounded hover:bg-stone-100 focus:outline-none focus:ring-2 focus:ring-amber-950 text-amber-950">
                <i class="bi bi-download text-xl" aria-hidden="true"></i>
            </button>
        </div>

    </div>
</template>
  
  <script setup>
  import { defineProps, computed, ref, onMounted, watch } from 'vue';
import instance from '../api/agent.ts';
import doc from '../assets/images/doc.webp'
import { useUserStore } from '../stores/user'

  const props = defineProps({
    file: Object
  })

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
        // fallback to default
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

  const downloadFile = () => {
    const fileId = props.file.id;
    // Replace 'instance' with your Axios or fetch instance for API calls
    instance.get(`/download/${fileId}`, { responseType: 'blob' })
        .then(response => {
            const url = window.URL.createObjectURL(new Blob([response.data]));
            const link = document.createElement('a');
            link.href = url;
            link.setAttribute('download', props.file.fileName);
            document.body.appendChild(link);
            link.click();
            link.remove(); // Clean up
        })
        .catch(error => {
            console.error('Error downloading file:', error);
        });
}
  </script>
  

  