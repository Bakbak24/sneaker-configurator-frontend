<template>
  <div>
    <h1>Login Page</h1>
    <form @submit.prevent="handleLogin">
      <input v-model="username" placeholder="Username" />
      <input type="password" v-model="password" placeholder="Password" />
      <button type="submit">Login</button>
    </form>
    <p v-if="errorMessage">{{ errorMessage }}</p>
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
