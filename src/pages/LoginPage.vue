<template>
  <div class="min-h-screen flex items-center justify-center bg-black text-white">
    <div class="bg-white text-black p-8 shadow-lg max-w-sm w-full">
      <div class="flex justify-center mb-6">
        <img src="/swear-logo.png" alt="Swear London Logo" class="w-52 h-auto" />
      </div>
      <form @submit.prevent="handleLogin" class="space-y-6">
        <div>
          <label for="username" class="block text-sm font-medium text-gray-700">Username</label>
          <input
            id="username"
            v-model="username"
            type="text"
            placeholder="Enter your username"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 bg-white text-black placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 sm:text-sm"
          />
        </div>
        <div>
          <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
          <input
            id="password"
            v-model="password"
            type="password"
            placeholder="Enter your password"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 bg-white text-black placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 sm:text-sm"
          />
        </div>
        <button
          type="submit"
          class="w-full bg-black text-white font-semibold py-3 px-4 border border-white hover:bg-gray-800 focus:outline-none focus:ring-2 focus:ring-gray-500"
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
import { useRouter } from 'vue-router'

export default {
  setup() {
    const username = ref('')
    const password = ref('')
    const errorMessage = ref('')
    const router = useRouter()

    const handleLogin = async () => {
      if (!username.value || !password.value) {
        errorMessage.value = 'Please enter both username and password.'
        return
      }

      try {
        const response = await fetch(
          'https://sneaker-configurator-backend.onrender.com/users/login',
          {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({ username: username.value, password: password.value }),
          },
        )

        const data = await response.json()

        if (data.status === 'success') {
          const decodedToken = JSON.parse(atob(data.data.token.split('.')[1]))
          console.log(decodedToken.role);

          if (decodedToken.role !== 'admin') {
            errorMessage.value = 'Access denied. Only admins can log in.'
            return
          }

          localStorage.setItem('token', data.data.token)
          router.push('/dashboard')
        } else {
          errorMessage.value = data.message || 'Invalid login credentials.'
        }
      } catch (error) {
        errorMessage.value = 'An error occurred. Please try again later.'
        console.error(error)
      }
    }

    return { username, password, errorMessage, handleLogin }
  },
}
</script>
