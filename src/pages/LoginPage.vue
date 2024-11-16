<template>
  <div
    class="min-h-screen flex items-center justify-center bg-gradient-to-r from-blue-500 to-blue-600"
  >
    <div class="bg-white p-8 rounded-lg shadow-lg max-w-sm w-full">
      <h1 class="text-3xl font-extrabold text-center text-gray-800 mb-6">Welcome Back</h1>
      <p class="text-center text-gray-600 mb-4">Login to access your account</p>
      <form @submit.prevent="handleLogin" class="space-y-6">
        <div>
          <label for="username" class="block text-sm font-medium text-gray-700"> Username </label>
          <input
            id="username"
            v-model="username"
            type="text"
            placeholder="Enter your username"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-500 sm:text-sm"
          />
        </div>
        <div>
          <label for="password" class="block text-sm font-medium text-gray-700"> Password </label>
          <input
            id="password"
            v-model="password"
            type="password"
            placeholder="Enter your password"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:border-blue-500 sm:text-sm"
          />
        </div>
        <button
          type="submit"
          class="w-full bg-blue-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-offset-2"
        >
          Login
        </button>
      </form>
      <p v-if="errorMessage" class="text-red-500 text-center mt-4">
        {{ errorMessage }}
      </p>
      <p class="text-sm text-center text-gray-500 mt-6">
        Don’t have an account?
        <a href="#" class="text-blue-500 hover:underline">Sign up</a>
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
        errorMessage.value = ''
      }
    }

    return { username, password, errorMessage, handleLogin }
  },
}
</script>
