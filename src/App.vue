<script setup>
import { ref, onMounted } from 'vue';
import Navbar from '@/components/Navbar.vue';

const isLoading = ref(true);

onMounted(() => {
  setTimeout(() => {
    isLoading.value = false;
  }, 1500);
});
</script>

<template>
  <div>
    <!-- Loading Animation -->
    <div
      v-if="isLoading"
      class="min-h-screen flex items-center justify-center bg-pink-50 text-pink-700 text-xl font-semibold animate-fade"
    >
      <div class="flex flex-col items-center space-y-3">
        <svg
          class="animate-spin h-10 w-10 text-pink-500"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
          <path
            class="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8v4l3-3-3-3v4a8 8 0 000 16v-4l-3 3 3 3v-4a8 8 0 01-8-8z"
          />
        </svg>
        <span class="text-sm animate-pulse">Warming up your cozy booth... 📸</span>
      </div>
    </div>

    <!-- Main App -->
    <div v-else>
      <Navbar />
      <RouterView />
    </div>
  </div>
</template>
