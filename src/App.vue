<template>
  <div>
    <TheHeader />
    <main>
      <router-view />
    </main>
  </div>
</template>
<script setup>
import TheHeader from "./components/TheHeader.vue";
</script>
<style lang="scss"></style>
