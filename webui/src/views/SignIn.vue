<template>
  <div class="flex justify-center mt-24">
    <div class="card bg-base-100 image-full shadow-xl">
      <figure>
        <img src="../assets//images/book-img.png" alt="Shoes" />
      </figure>
      <div class="card-body content-center grid">
        <h1 class="text-justify text-6xl">Let's Share!!!</h1>
      </div>
    </div>
    <div class="border p-16 rounded-md shadow-xl">
      <h1 class="text-center text-4xl mb-6 text-amber-950">Sign In</h1>
      <form @submit.prevent="onSubmit" class="mt-4" novalidate>
        <label for="email" class="block mb-2">Email</label>
        <input
          id="email"
          type="email"
          placeholder="daisy@site.com"
          class="input input-bordered flex items-center gap-2 mb-4 grow"
          v-model="email"
          required
          autocomplete="username"
          :aria-invalid="!!error"
          autofocus
        />

        <label for="password" class="block mb-2">Password</label>
        <input
          id="password"
          class="input input-bordered flex items-center gap-2 mb-4"
          type="password"
          placeholder="password" v-model="password"
          required
          autocomplete="current-password"
        />

        <p v-if="error" class="text-sm text-red-600 mb-4">{{ error }}</p>

        <button type="submit" :disabled="loading" class="btn btn-block bg-amber-950 text-white text-2xl rounded-md mb-2">
          <span v-if="!loading">Sign In</span>
          <span v-else>Signing in...</span>
        </button>
      </form>
      <p class="mt-4">Don't have an account? <RouterLink to="/signup" class="link">sign up</RouterLink></p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import instance from '../api/agent.ts'
import { useUserStore } from '../stores/user'
import { useRouter } from 'vue-router'

const email = ref("")
const password = ref("")
const error = ref("")
const loading = ref(false)
const userStore = useUserStore()
const router = useRouter()

const loginUser = async () => {
  loading.value = true
  error.value = ''
  try {
    const response = await instance.post('User/login', { email: email.value, password: password.value });
    const userData = response.data;

    userStore.setUser(userData);
    userStore.setAccessToken(userData.token)

    router.push('/dashboard');
  } catch (err) {
    error.value = err?.response?.data?.message || 'Login failed. Check credentials.'
    console.error('Error logging in:', err);
  } finally {
    loading.value = false
  }
};

const onSubmit = () => {
  if (!email.value || !password.value) {
    error.value = 'Please provide email and password.'
    return
  }
  loginUser()
}
</script>
