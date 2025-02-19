<script setup lang="ts">
import { RouterView } from 'vue-router'
</script>

<template>
  <!-- <header>
    <nav>
      <RouterLink to="/">Widget</RouterLink>
      <RouterLink to="/about">About</RouterLink>
    </nav>
  </header> -->

  <RouterView />
</template>

<style scoped>
header,
nav {
  width: 100%;
}

nav {
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a {
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}
</style>
