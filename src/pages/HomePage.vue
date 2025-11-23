<!-- src/pages/HomePage.vue -->
<template>
  <div class="home-page">
    <SubnetCalculator />
  </div>
</template>

<script setup lang="ts">
import SubnetCalculator from '../components/SubnetCalculator.vue';
</script>

<style scoped>
.home-page {
  padding: 1rem;
}
</style>