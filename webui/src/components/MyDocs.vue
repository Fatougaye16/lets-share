<template>
  <div class="pr-6 py-6 mr-12 h-screen">
    <div class="text-3xl text-amber-950 font-bold mb-8">
      <h1>My Documents</h1>
    </div>
    <div class="flex justify-between">
      <div>
        <label class="input input-bordered flex items-center gap-2 mb-12">
          <input type="text" class="grow" placeholder="Search" v-model="searchTerm" aria-label="Search documents" />
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="h-4 w-4 opacity-70">
            <path fill-rule="evenodd"
              d="M9.965 11.026a5 5 0 1 1 1.06-1.06l2.755 2.754a.75.75 0 1 1-1.06 1.06l-2.755-2.754ZM10.5 7a3.5 3.5 0 1 1-7 0 3.5 3.5 0 0 1 7 0Z"
              clip-rule="evenodd" />
          </svg>
        </label>
      </div>

      <div>
        <button class="btn bg-amber-950 text-white w-64 rounded-lg" @click="openDialog">
          <i class="bi bi-upload" aria-hidden="true"></i>
          <span class="ms-2">Upload file</span>
        </button>
        <dialog ref="dialogRef" class="modal" id="my_modal_1">
          <div class="modal-box">
            <h3 class="text-lg font-bold ">New document</h3>
            <hr />
            <div class="flex items-center justify-center w-full mt-6 p-6">
              <label for="dropzone-file"
                class="flex flex-col items-center justify-center w-full h-64 border-2 border-amber-950 border-dashed rounded-lg cursor-pointer">
                <div class="flex flex-col items-center relative justify-center pt-5 pb-6">
                  <i class="bi bi-cloud-arrow-up-fill text-amber-950 text-4xl"></i>

                  <p class="mb-2 text-sm "><span class="font-semibold">Click to upload</span> or drag and drop</p>
                  <p class="text-xs ">SVG, PNG, JPG or GIF (MAX. 800x400px)</p>
                </div>
                <p class="absolute mt-36 px-8 py-2 rounded-md text-amber-950 bg-stone-200">Browse</p>
                <input id="dropzone-file" type="file" class="hidden" @change="handleFileUpload" />
              </label>
            </div>
            <div class="modal-action">
              <form method="dialog">
                <button class="btn w-40 rounded-md text-amber-950" @click.prevent="closeDialog">Cancel</button>
              </form>
              <button class="btn w-40 rounded-md bg-amber-950 text-white" @click="uploadFile">Upload</button>
            </div>
          </div>
        </dialog>
      </div>
    </div>
    <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
      <div v-for="file in filteredUploads" :key="file.id">
        <Card :file="file" />
      </div>
    </div>
  </div>
</template>
<script setup>
import Card from './DocsCard.vue'
import { useUserStore } from '../stores/user'
import { computed, ref } from 'vue'
import instance from '../api/agent.ts'

const userStore = useUserStore()
const user = computed(() => userStore.user)
const file = ref(null)
const dialogRef = ref(null)
const searchTerm = ref('')

const token = computed(() => userStore.token)
const handleFileUpload = (event) => {
  file.value = event.target.files[0]
}

const openDialog = () => {
  dialogRef.value?.showModal()
}

const closeDialog = () => {
  dialogRef.value?.close()
}

const uploadFile = async () => {
  if (file.value) {
    const formData = new FormData();
    formData.append('file', file.value);
    
    try {
      await instance.post('/uploadingtodb', formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
           'Authorization': `Bearer ${token.value}`
        }
      });
      console.log('File uploaded successfully:', file.value);
      file.value = null;
      closeDialog()
    } catch (error) {
      console.error('Error uploading file:', error);
    }
  } else {
    console.log('No file selected');
  }
}

const filteredUploads = computed(() => {
  if (!user.value?.uploads) return []
  if (!searchTerm.value) return user.value.uploads
  return user.value.uploads.filter(f => f.fileName.toLowerCase().includes(searchTerm.value.toLowerCase()))
})
</script>
