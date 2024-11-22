<script setup>
import { RouterLink, RouterView } from 'vue-router'
import home from './components/Home.vue'
</script>

<template>
  <header>

    <div class="container text-center" style="display:none;">
      <nav>
        <RouterLink to="/" class=" p-1">Home</RouterLink>
        <RouterLink to="/carrinho">Carrinho</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>