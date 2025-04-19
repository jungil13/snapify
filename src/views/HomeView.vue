<template>
  <div class="min-h-screen flex flex-col items-center justify-center text-center bg-white relative overflow-hidden px-4 sm:mt-1 mt-12">
    <!-- Frosty Radial Background -->
    <div class="absolute inset-0 bg-gradient-radial from-white via-pink-100/50 to-transparent rounded-full w-[700px] h-[700px] top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 z-0 blur-3xl opacity-60"></div>

    <!-- Falling Cherry Blossoms -->
    <div v-for="n in 15" :key="n" class="cherry-blossom" />

    <!-- Content -->
    <div class="z-10 fade-in-up transition-all duration-700 opacity-0 translate-y-8">
      <h1 class="text-4xl md:text-5xl font-extrabold mb-4 text-pink-700 drop-shadow-pink animate-zoom">
        Snapyfy - Free Cozy Photo Booth
      </h1>
      <p class="mb-3 text-lg text-gray-700">
        Your warmest photobooth from home — snap, style, and share 💕
      </p>
      <p class=" text-sm text-gray-500">With filters, stickers, and cozy frames ✨</p>
      <p class="text-sm text-gray-500 mb-8">made with 💜 by <span class="bg-pink-100 px-2 py-0.5 rounded-full">𝓙𝓾𝓷 𝓖𝓲𝓵</span></p>
      <!-- Photobooth Strip -->
      <div class="bg-white rounded-2xl p-4 shadow-sm shadow-black w-44 mx-auto mb-6 space-y-2 relative">
        <div class="absolute inset-0 bg-gradient-to-r from-pink-500/60 to-pink-300/60 rounded-2xl blur-sm z-[-1]"></div>
        <img src="https://i.pinimg.com/736x/12/20/b5/1220b562794722a481de6e359b5b8593.jpg" alt="snap" class="rounded-md w-full object-cover" />
        <img src="https://i.pinimg.com/736x/10/b5/8d/10b58d27a6f60915e048e65b9618728e.jpg" alt="snap" class="rounded-md w-full object-cover" />
        <img src="https://i.pinimg.com/736x/a2/a7/17/a2a717c69ab0f64f74ee7c11d2aa634c.jpg" alt="snap" class="rounded-md w-full object-cover" />
        <div class="text-[10px] text-gray-400 mt-1">Snapyfy — {{ timestamp }}</div>
      </div>

      <button
        @click="startPhotobooth"
        :disabled="loading"
        class="relative px-6 py-2 bg-pink-500 text-white font-semibold rounded-full shadow hover:bg-pink-600 transition animate-bounce"
      >
        <span v-if="!loading">START</span>
        <span v-else class="flex items-center gap-2">
          <svg class="animate-spin h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor"
              d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z" />
          </svg>
          Loading...
        </span>
      </button>

      <!-- Footer -->
      <div class="mt-10 text-xs text-gray-400">
        Snapyfy Booth — Cozy photobooth vibes <br />
        made with 💜 by <span class="bg-pink-100 px-2 py-0.5 rounded-full">𝓙𝓾𝓷 𝓖𝓲𝓵</span> <br />
        © 2025 Snapyfy. All rights reserved.
      </div>
    </div>
  </div>
  <RouterView />
</template>
<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, RouterView } from 'vue-router';
import { useRouter } from 'vue-router';

const router = useRouter();

const timestamp = ref(new Date().toLocaleString());
const loading = ref(false);

const startPhotobooth = () => {
  loading.value = true;

  // Simulate loading effect (can be removed if actual loading time exists)
  setTimeout(() => {
    router.push('/photobooth');
  }, 1200);
};
onMounted(() => {
  const elements = document.querySelectorAll('.fade-in-up');
  elements.forEach((el, index) => {
    setTimeout(() => {
      el.classList.add('opacity-100', 'translate-y-0');
    }, index * 200);
  });
});
</script>
<style scoped>
.bg-gradient-radial {
  background: radial-gradient(circle, var(--tw-gradient-stops));
}

.drop-shadow-pink {
  filter: drop-shadow(1px 1px 3px rgba(255, 182, 193, 0.5));
}

@keyframes zoomy {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}
.animate-zoom {
  animation: zoomy 3s ease-in-out infinite;
}

/* Falling Cherry Blossoms */
@keyframes fall {
  0% {
    transform: translateY(-100px) rotate(0deg);
    opacity: 1;
  }
  100% {
    transform: translateY(120vh) rotate(360deg);
    opacity: 0;
  }
}

.cherry-blossom {
  position: absolute;
  top: -10px;
  width: 24px;
  height: 24px;
  background-image: url('../assets/images/che.png');
  background-size: cover;
  animation: fall 10s linear infinite;
  opacity: 0.8;
  z-index: 0;
}

/* Randomize left position */
.cherry-blossom:nth-child(1) { left: 5%; animation-delay: 0s; }
.cherry-blossom:nth-child(2) { left: 15%; animation-delay: 2s; }
.cherry-blossom:nth-child(3) { left: 25%; animation-delay: 4s; }
.cherry-blossom:nth-child(4) { left: 35%; animation-delay: 1s; }
.cherry-blossom:nth-child(5) { left: 45%; animation-delay: 3s; }
.cherry-blossom:nth-child(6) { left: 55%; animation-delay: 5s; }
.cherry-blossom:nth-child(7) { left: 65%; animation-delay: 2.5s; }
.cherry-blossom:nth-child(8) { left: 75%; animation-delay: 4.5s; }
.cherry-blossom:nth-child(9) { left: 85%; animation-delay: 1.5s; }
.cherry-blossom:nth-child(10) { left: 95%; animation-delay: 3.5s; }
.cherry-blossom:nth-child(11) { left: 10%; animation-delay: 2.2s; }
.cherry-blossom:nth-child(12) { left: 30%; animation-delay: 1.8s; }
.cherry-blossom:nth-child(13) { left: 50%; animation-delay: 3.2s; }
.cherry-blossom:nth-child(14) { left: 70%; animation-delay: 0.8s; }
.cherry-blossom:nth-child(15) { left: 90%; animation-delay: 4.1s; }
</style>
