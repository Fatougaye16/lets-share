<template>
  <div class="flex justify-center mt-24">
    <div class="card bg-base-100 image-full shadow-xl">
      <figure>
        <img src="../assets//images/book-img.png" alt="Shoes" />
      </figure>
      <div class="card-body grid content-center">
        <h1 class=" text-justify text-6xl">Let's Share!!!</h1>
      </div>
    </div>
    <div class=" border p-16 rounded-md shadow-xl">
      <h1 class=" text-center text-4xl mb-6 text-amber-950">Sign Up</h1>
      <form @submit.prevent="onSubmit" class=" mt-4" novalidate>
        <label for="name" class="block mb-2">Name</label>
        <input id="name" class="input input-bordered flex items-center gap-2 mb-4" type="text" placeholder="Enter your fullname"
          v-model="userName" required />

        <label for="email" class="block mb-2">Email</label>
        <input id="email" type="email" placeholder="daisy@site.com"
          class="input input-bordered flex items-center gap-2 mb-4 grow" v-model="email" required />

        <label for="password" class="block mb-2">Password</label>
        <input id="password" class="input input-bordered flex items-center gap-2 mb-4" type="password" placeholder="password"
          v-model="password" required />

        <label for="confirmPassword" class="block mb-2">Confirm Password</label>
        <input id="confirmPassword" class="input input-bordered flex items-center gap-2 mb-4" type="password"
          placeholder="confirm password" v-model="confirmPassword" required />

        <p v-if="error" class="text-sm text-red-600 mb-4">{{ error }}</p>

        <button type="submit" class="btn btn-block bg-amber-950 text-white text-2xl rounded-md" :disabled="loading">
          <span v-if="!loading">Sign Up</span>
          <span v-else>Creating...</span>
        </button>
      </form>
      <p class="mt-4">Already have an account? <RouterLink to="/" class=" link">sign in</RouterLink>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import instance from '../api/agent.ts'
import { useUserStore } from '../stores/user'
import { useRouter } from 'vue-router'
const userName = ref("")
const email = ref("")
const password = ref("")
const confirmPassword = ref("")
const error = ref("")
const loading = ref(false)
const userStore = useUserStore()
const router = useRouter()

const createUser = async () => {
  loading.value = true
  error.value = ''
  try {
    const response = await instance.post('User/register', { userName: userName.value, email: email.value, password: password.value });
    const userData = response.data;

    userStore.setUser(userData);
    router.push('/dashboard');
  } catch (err) {
    error.value = err?.response?.data?.message || 'Registration failed.'
    console.error('Error signing up:', err);
  } finally {
    loading.value = false
  }
};

const onSubmit = () => {
  if (!userName.value || !email.value || !password.value || !confirmPassword.value) {
    error.value = 'Please fill all fields.'
    return
  }
  if (password.value !== confirmPassword.value) {
    error.value = 'Passwords do not match.'
    return
  }
  createUser()
}
</script>
