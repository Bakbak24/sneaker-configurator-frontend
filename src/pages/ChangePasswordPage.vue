<template>
  <div class="min-h-screen flex bg-gray-100">
    <!-- Sidebar -->
    <Sidebar />
    <!-- Change password form -->
    <div class="flex-1 flex items-center justify-center bg-gray-100">
      <div class="bg-white p-8 shadow-lg max-w-sm w-full rounded">
        <h1 class="text-xl font-bold text-gray-800 mb-4">Change Password</h1>
        <form @submit.prevent="handleChangePassword" class="space-y-6">
          <div>
            <label for="newPassword" class="block text-sm font-medium text-gray-700">New Password</label>
            <input
              id="newPassword"
              v-model="newPassword"
              type="password"
              placeholder="Enter your new password"
              class="mt-1 block w-full px-4 py-2 border border-gray-300 bg-white text-black placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 sm:text-sm"
            />
            <p v-if="errorNewPassword" class="text-red-500 text-sm mt-1">{{ errorNewPassword }}</p>
          </div>
          <div>
            <label for="confirmPassword" class="block text-sm font-medium text-gray-700">Confirm New Password</label>
            <input
              id="confirmPassword"
              v-model="confirmPassword"
              type="password"
              placeholder="Confirm your new password"
              class="mt-1 block w-full px-4 py-2 border border-gray-300 bg-white text-black placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-gray-500 sm:text-sm"
            />
            <p v-if="errorConfirmPassword" class="text-red-500 text-sm mt-1">{{ errorConfirmPassword }}</p>
          </div>
          <button
            type="submit"
            class="w-full bg-black text-white font-semibold py-3 px-4 hover:bg-gray-800 focus:outline-none focus:ring-2 focus:ring-gray-500"
          >
            Change Password
          </button>
        </form>
        <p v-if="errorMessage" class="text-red-500 text-center mt-4">{{ errorMessage }}</p>
        <p v-if="successMessage" class="text-green-500 text-center mt-4">{{ successMessage }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import Sidebar from '../components/Sidebar.vue';

export default {
  components: { Sidebar },
  setup() {
    const newPassword = ref('');
    const confirmPassword = ref('');
    const errorMessage = ref('');
    const successMessage = ref('');
    const errorNewPassword = ref('');
    const errorConfirmPassword = ref('');
    const router = useRouter();

    const decodeUserId = () => {
      const token = localStorage.getItem('token');
      if (token) {
        try {
          const payload = JSON.parse(atob(token.split('.')[1]));
          return payload.uid;
        } catch (error) {
          console.error('Error decoding token:', error);
          return null;
        }
      }
      return null;
    };

    const handleChangePassword = async () => {
      errorNewPassword.value = '';
      errorConfirmPassword.value = '';
      errorMessage.value = '';
      successMessage.value = '';

      // Basic validations
      if (!newPassword.value) {
        errorNewPassword.value = 'New password is required.';
        return;
      }
      if (newPassword.value.length < 8) {
        errorNewPassword.value = 'New password must be at least 8 characters.';
        return;
      }
      if (!/[A-Z]/.test(newPassword.value)) {
        errorNewPassword.value = 'New password must contain at least one uppercase letter.';
        return;
      }
      if (newPassword.value !== confirmPassword.value) {
        errorConfirmPassword.value = 'Passwords do not match.';
        return;
      }

      const userId = decodeUserId();
      if (!userId) {
        errorMessage.value = 'User ID not found. Please log in again.';
        return;
      }

      try {
        const response = await fetch('https://sneaker-configurator-backend.onrender.com/users/updatePassword', {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
          body: JSON.stringify({
            userId,
            newPassword: newPassword.value,
          }),
        });

        const data = await response.json();

        if (data.status === 'success') {
          successMessage.value = 'Password changed successfully.';
          setTimeout(() => {
            router.push('/dashboard');
          }, 2000);
        } else {
          errorMessage.value = data.message || 'Failed to change password.';
        }
      } catch (error) {
        console.error('Error changing password:', error);
        errorMessage.value = 'An error occurred. Please try again later.';
      }
    };

    return {
      newPassword,
      confirmPassword,
      errorMessage,
      successMessage,
      errorNewPassword,
      errorConfirmPassword,
      handleChangePassword,
    };
  },
};
</script>

<style scoped>
.text-green-500 {
  color: #10b981;
}
.text-red-500 {
  color: #ef4444;
}
</style>
