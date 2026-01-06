<template>
  <div class="h-screen overflow-hidden">
    <SideNav />
    <main class="h-full overflow-y-auto" :style="{ paddingLeft: 'calc(var(--sidebar-left, 256px) + 1rem)', transition: 'padding-left 200ms ease' }">
      <RouterView />
    </main>
  </div>
</template>



<script setup>
import { RouterView } from 'vue-router';
import SideNav from '../components/SideNav.vue';
</script>
