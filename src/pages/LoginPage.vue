<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <div class="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
      <h1 class="text-2xl font-bold text-center mb-4">Login</h1>
      <form @submit.prevent="handleLogin" class="space-y-4">
        <div>
          <label for="username" class="block text-sm font-medium text-gray-700"> Username </label>
          <input
            id="username"
            v-model="username"
            type="text"
            placeholder="Enter your username"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
          />
        </div>
        <div>
          <label for="password" class="block text-sm font-medium text-gray-700"> Password </label>
          <input
            id="password"
            v-model="password"
            type="password"
            placeholder="Enter your password"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
          />
        </div>
        <button
          type="submit"
          class="w-full bg-blue-500 text-white py-2 px-4 rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
        >
          Login
        </button>
      </form>
      <p v-if="errorMessage" class="text-red-500 text-center mt-4">
        {{ errorMessage }}
      </p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const username = ref('')
    const password = ref('')
    const errorMessage = ref('')

    const handleLogin = () => {
      if (!username.value || !password.value) {
        errorMessage.value = 'Please enter both username and password.'
      } else {
        console.log('Login attempt:', { username: username.value, password: password.value })
        errorMessage.value = '' // Reset error message
      }
    }

    return { username, password, errorMessage, handleLogin }
  },
}
</script>
