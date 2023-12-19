<template>
  <div class="common-layout">
    <div class="h-screen flex flex-col">
      <page-header />
      <page-main-content />
    </div>
  </div>
</template>

<script setup>
import PageHeader from "./components/PageHeader.vue"
import PageMainContent from "./components/PageMainContent.vue"
</script>

<style scoped></style>
