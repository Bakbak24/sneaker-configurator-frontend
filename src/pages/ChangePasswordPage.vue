<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <Sidebar />
    <div class="bg-white p-8 shadow-lg max-w-sm w-full">
      <h1 class="text-xl font-bold text-gray-800 mb-4">Change Password</h1>
      <form @submit.prevent="handleChangePassword" class="space-y-6">
        <div>
          <label for="currentPassword" class="block text-sm font-medium text-gray-700">Current Password</label>
          <input
            id="currentPassword"
            v-model="currentPassword"
            type="password"
            placeholder="Enter your current password"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded bg-gray-50 text-gray-800 focus:ring-2 focus:ring-gray-500 sm:text-sm"
          />
        </div>
        <div>
          <label for="newPassword" class="block text-sm font-medium text-gray-700">New Password</label>
          <input
            id="newPassword"
            v-model="newPassword"
            type="password"
            placeholder="Enter your new password"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded bg-gray-50 text-gray-800 focus:ring-2 focus:ring-gray-500 sm:text-sm"
          />
        </div>
        <div>
          <label for="confirmPassword" class="block text-sm font-medium text-gray-700">Confirm New Password</label>
          <input
            id="confirmPassword"
            v-model="confirmPassword"
            type="password"
            placeholder="Confirm your new password"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded bg-gray-50 text-gray-800 focus:ring-2 focus:ring-gray-500 sm:text-sm"
          />
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
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import Sidebar from '../components/Sidebar.vue';

export default {
  components: { Sidebar },
  setup() {
    const currentPassword = ref('');
    const newPassword = ref('');
    const confirmPassword = ref('');
    const errorMessage = ref('');
    const successMessage = ref('');
    const router = useRouter();

    const handleChangePassword = async () => {
      if (!currentPassword.value || !newPassword.value || !confirmPassword.value) {
        errorMessage.value = 'All fields are required.';
        successMessage.value = '';
        return;
      }

      if (newPassword.value !== confirmPassword.value) {
        errorMessage.value = 'New passwords do not match.';
        successMessage.value = '';
        return;
      }

      try {
        const response = await fetch('https://sneaker-configurator-backend.onrender.com/users/change-password', {
          method: 'PATCH',
          headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${localStorage.getItem('token')}`,
          },
          body: JSON.stringify({
            currentPassword: currentPassword.value,
            newPassword: newPassword.value,
          }),
        });

        const data = await response.json();

        if (data.status === 'success') {
          successMessage.value = 'Password changed successfully.';
          errorMessage.value = '';
          setTimeout(() => {
            router.push('/dashboard');
          }, 2000);
        } else {
          errorMessage.value = data.message || 'Failed to change password.';
          successMessage.value = '';
        }
      } catch (error) {
        console.error('Error changing password:', error);
        errorMessage.value = 'An error occurred. Please try again later.';
        successMessage.value = '';
      }
    };

    return {
      currentPassword,
      newPassword,
      confirmPassword,
      errorMessage,
      successMessage,
      handleChangePassword,
    };
  },
};
</script>

<style scoped>
</style>
