<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center justify-center">
    <h1 class="text-3xl font-bold text-gray-800 mb-4">Welcome to the Dashboard!</h1>
    <button
      @click="logout"
      class="bg-red-500 text-white py-2 px-4 rounded-lg hover:bg-red-600 focus:outline-none"
    >
      Logout
    </button>
  </div>
</template>

<script>
import { useRouter } from 'vue-router'

export default {
  setup() {
    const router = useRouter()

    const logout = () => {
      localStorage.removeItem('token')
      router.push('/')
    }

    return { logout }
  },
}
</script>
