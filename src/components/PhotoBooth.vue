<template>
    <div class="mt-20 p-6 bg-white text-gray-900 rounded-xl shadow-md shadow-black max-w-6xl mx-auto space-y-8">
      <h1 class="text-3xl font-semibold font-serif text-blue-600 flex items-center gap-2">
        <CameraIcon class="w-8 h-8 text-blue-600 "  />
        Snapify Photobooth
      </h1>
  
      <div class="flex flex-col md:flex-row gap-8 w-full justify-center items-start">
        <div class="flex flex-col items-center gap-6 w-full md:w-1/2">
          <div class="relative w-full max-w-md">
            <video
              ref="video"
              autoplay
              playsinline
              :playsinline="true"
              class="rounded-xl border-4 border-blue-300 w-full h-auto transition-all duration-300"
              :class="selectedFilter"
            />
            <div
              v-if="countdown > 0"
              class="absolute inset-0 flex items-center justify-center text-gray-100 text-6xl font-extrabold  bg-opacity-70 rounded-xl animate-fadeIn z-10"
            >
              {{ countdown }}
            </div>
  
            <div
              v-if="showFlash"
              class="absolute inset-0 bg-white opacity-80 animate-flash z-20"
            ></div>
          </div>
  
          <div class="flex flex-wrap gap-4 justify-center">
            <select v-model="selectedLayout" class="select-box">
              <option value="2">2 Photos</option>
              <option value="3">3 Photos</option>
              <option value="4">4 Photos</option>
              <option value="6">6 Photos</option>
            </select>
  
            <select v-model="countdownSeconds" class="select-box">
              <option value="3">3s Countdown</option>
              <option value="5">5s Countdown</option>
              <option value="10">10s Countdown</option>
            </select>
  
            <select v-model="selectedFilter" class="select-box">
              <option value="">No Filter</option>
              <option value="filter-grayscale">Grayscale</option>
              <option value="filter-sepia">Sepia</option>
              <option value="filter-contrast">High Contrast</option>
            </select>
          </div>
  
          <div class="flex flex-wrap gap-4 justify-center mt-2">
            <button @click="startCamera" class="btn-blue">Start Camera</button>
            <button @click="startCapture" :disabled="capturing" class="btn-green">
              Start Capture
            </button>
          </div>
        </div>
  
        <div class=" rounded-xl shadow-sm shadow-black p-4 flex flex-col items-center w-full md:w-1/2 space-y-4">
          <h2 class="text-lg font-semibold text-blue-600">Photo Strip Preview</h2>
  
          <div
            v-if="snaps.length"
            ref="photoStripRef"
            class="bg-white p-4 rounded space-y-2 shadow-sm shadow-black w-full max-w-xs"
          >
            <img
              v-for="(snap, i) in snaps"
              :key="i"
              :src="snap"
              class="rounded-md w-full object-cover"
            />
            <div class="text-[10px] text-gray-400 mt-1 text-center">Snapyfy — {{ timestamp }}</div>
          </div>
  
          <div v-else class="text-sm text-gray-400">No snaps captured yet.</div>
  
          <div class="flex flex-wrap gap-4 justify-center mt-2">
            <button @click="printStrip" class="btn-yellow">
              <PrinterIcon class="w-5 h-5 inline mr-2" /> Print
            </button>
            <button @click="shareToFacebook" class="btn-blue">
              <FacebookIcon class="w-5 h-5 inline mr-2" /> Facebook
            </button>
            <button @click="shareToInstagram" class="btn-pink">
              <InstagramIcon class="w-5 h-5 inline mr-2" /> Instagram
            </button>
            <button @click="downloadStrip" class="btn-purple">
              <DownloadIcon class="w-5 h-5 inline mr-2" /> Download
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  
  <script setup>
  import { ref, onBeforeUnmount } from 'vue'
  import { CameraIcon, PrinterIcon, DownloadIcon, FacebookIcon, InstagramIcon } from 'lucide-vue-next' // install via: npm i lucide-vue-next
  import * as domtoimage from 'dom-to-image'; // Import dom-to-image
  
  const video = ref(null)
  const photoStripRef = ref(null)
  const snaps = ref([])
  const stream = ref(null)
  const countdown = ref(0)
  const capturing = ref(false)
  const selectedLayout = ref(4)
  const countdownSeconds = ref(3)
  const timestamp = ref(new Date().toLocaleString())
  const selectedFilter = ref('')
  const showFlash = ref(false)
  
  const flashEffect = () => {
    showFlash.value = true
    setTimeout(() => (showFlash.value = false), 150)
  }
  
  const playShutterSound = () => {
    const audio = new Audio('/shutter.mp3') // Add your shutter sound file path
    audio.play()
  }
  
  const downloadStrip = () => {
  if (!photoStripRef.value) return;

  domtoimage.toPng(photoStripRef.value)
    .then((dataUrl) => {
      const link = document.createElement('a');
      link.href = dataUrl;
      link.download = `snapyfy_strip_${Date.now()}.png`;
      link.click();
    })
    .catch((error) => {
      console.error('Error capturing photo strip:', error);
      alert('Error saving the photo strip.');
    });
};

  
  const printStrip = () => {
    console.log("Printing...");
  
    try {
      if (!photoStripRef.value) return;
      const photoStripHtml = photoStripRef.value.innerHTML;
  
      const printWindow = window.open('', '_blank');
      printWindow.document.write(`
        <html>
          <head>
            <style>
              body {
                font-family: Arial, sans-serif;
                margin: 0;
                padding: 0;
                text-align: center;
              }
              .print-container {
                display: inline-block;
                background-color: #fff;
                padding: 10px;
                border-radius: 12px;
                box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
              }
              .print-container img {
                width: 100%;
                border-radius: 8px;
                margin-bottom: 8px;
              }
              .print-container .text-gray-400 {
                color: #a0aec0; /* Tailwind gray-400 equivalent */
                font-size: 10px;
                margin-top: 4px;
                text-align: center;
              }
            </style>
          </head>
          <body>
            <div class="print-container">
              ${photoStripHtml}
            </div>
          </body>
        </html>
      `);
      printWindow.document.close();
      printWindow.onload = () => {
        printWindow.print();
        printWindow.close();
      };
    } catch (error) {
      console.error("Error printing strip:", error);
    }
  };
  
  const shareToFacebook = () => {
    const url = `https://www.facebook.com/sharer/sharer.php?u=${encodeURIComponent(window.location.href)}`;
    window.open(url, '_blank', 'width=600,height=400');
  };
  
  const shareToInstagram = () => alert('Sharing to Instagram...')
  
  const startCamera = async () => {
    try {
      stream.value = await navigator.mediaDevices.getUserMedia({ video: true })
      video.value.srcObject = stream.value
    } catch (err) {
      alert('Error accessing camera: ' + err.message)
    }
  }
  
  const captureSnap = () => {
    const canvas = document.createElement('canvas')
    canvas.width = video.value.videoWidth
    canvas.height = video.value.videoHeight
    const context = canvas.getContext('2d')
    context.drawImage(video.value, 0, 0)
    snaps.value.push(canvas.toDataURL('image/png'))
    playShutterSound()
  }
  
  const startCapture = async () => {
    capturing.value = true
    snaps.value = []
    for (let i = 0; i < selectedLayout.value; i++) {
      await runCountdown()
      flashEffect()
      captureSnap()
    }
    capturing.value = false
  }
  
  const runCountdown = () => {
    return new Promise((resolve) => {
      countdown.value = countdownSeconds.value
      const interval = setInterval(() => {
        countdown.value--
        if (countdown.value <= 0) {
          clearInterval(interval)
          resolve()
        }
      }, 1000)
    })
  }
  
  onBeforeUnmount(() => {
    if (stream.value) {
      stream.value.getTracks().forEach((track) => track.stop())
    }
  })
  </script>
  
  
  <style scoped>
  /* Filters */
  .filter-grayscale {
    filter: grayscale(100%);
  }
  .filter-sepia {
    filter: sepia(100%);
  }
  .filter-contrast {
    filter: contrast(150%);
  }
  
  /* Fade animation */
  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  .animate-fadeIn {
    animation: fadeIn 0.5s ease-in-out;
  }
  
  /* Flash animation */
  @keyframes flash {
    0% {
      opacity: 0.8;
    }
    50% {
      opacity: 0;
    }
    100% {
      opacity: 0.8;
    }
  }
  .animate-flash {
    animation: flash 0.15s ease-in-out;
  }
  
  /* Basic button styles */
  .btn-blue {
    background-color: #3b82f6; /* Tailwind blue-500 */
    color: #ffffff;
    font-weight: bold;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
  }
  .btn-blue:hover {
    background-color: #2563eb; /* Tailwind blue-700 */
  }
  
  .btn-green {
    background-color: #4ade80; /* Tailwind green-500 */
    color: #ffffff;
    font-weight: bold;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
  }
  .btn-green:hover {
    background-color: #22c55e; /* Tailwind green-700 */
  }
  .btn-green:disabled {
    background-color: #d1d5db; /* Tailwind gray-300 - adjust as needed */
    cursor: not-allowed;
  }
  
  .btn-yellow {
    background-color: #facc15; /* Tailwind yellow-500 */
    color: #ffffff;
    font-weight: bold;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
  }
  .btn-yellow:hover {
    background-color: #eab308; /* Tailwind yellow-700 */
  }
  
  .btn-pink {
    background-color: #ec4899; /* Tailwind pink-500 */
    color: #ffffff;
    font-weight: bold;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
  }
  .btn-pink:hover {
    background-color: #db2777; /* Tailwind pink-700 */
  }
  
  .btn-purple {
    background-color: #8b5cf6; /* Tailwind purple-500 */
    color: #ffffff;
    font-weight: bold;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
  }
  .btn-purple:hover {
    background-color: #7c3aed; /* Tailwind purple-700 */
  }
  
  /* Basic select box styles */
  .select-box {
    display: block;
    width: 100%;
    padding: 0.5rem 0.75rem;
    font-size: 1rem;
    line-height: inherit;
    border: 1px solid #d1d5db; /* Tailwind gray-300 */
    border-radius: 0.25rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05); /* Tailwind shadow-sm */
    color: #374151; /* Tailwind gray-700 */
  }
  .select-box:focus {
    outline: 2px solid transparent;
    outline-offset: 2px;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5); /* Tailwind blue-500 with opacity */
    border-color: #3b82f6; /* Tailwind blue-500 */
  }
  </style>