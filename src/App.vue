<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header class="flex items-center border-b-2">
    <RouterLink to="/" class="mb-2">
      <img alt="Vue logo" class="w-48" src="@/assets/co2-emission-checker-icon.png"
    /></RouterLink>

    <nav class="ml-auto">
      <RouterLink to="/" class="mr-4 p-3 font-bold rounded-sm hover:bg-green-500 hover:text-white"
        >Home</RouterLink
      >
      <RouterLink
        to="/about"
        class="mr-4 p-3 font-bold rounded-sm hover:bg-green-500 hover:text-white"
        >About</RouterLink
      >
      <RouterLink to="/checker" class="p-3 font-bold rounded-sm hover:bg-green-500 hover:text-white"
        >Checker</RouterLink
      >
    </nav>
  </header>

  <RouterView />
</template>
