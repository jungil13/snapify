<template>
    <nav class="fixed top-0 left-0 w-full z-50 flex justify-center py-4 px-4 sm:px-6 bg-transparent ">
      <div class="bg-white shadow-md shadow-black px-6 py-2 rounded-full flex gap-4 sm:gap-6 items-center">
        <router-link to="/" class="text-gray-600 hover:text-pink-500 text-sm sm:text-base font-medium">Home</router-link>
        <router-link to="/about" class="text-gray-600 hover:text-pink-500 text-sm sm:text-base font-medium">About</router-link>
        <router-link to="/faq" class="text-gray-600 hover:text-pink-500 text-sm sm:text-base font-medium">FAQ</router-link>
        <router-link to="/privacy" class="text-gray-600 hover:text-pink-500 text-sm sm:text-base font-medium">Privacy Policy</router-link>
      </div>
    </nav>
  </template>
  