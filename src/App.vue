<script setup>
  import {
    RouterView
  } from 'vue-router';
  import SiteNavigation from './components/SiteNavigation.vue';
</script>

<template>
  <div class=" flex flex-col bg-weather-primary min-h-screen font-Roboto">
    <SiteNavigation></SiteNavigation>
    <RouterView class=" flex-1" v-slot="{Component}">
      <Transition name="page">
        <component :is="Component"></component>
      </Transition>

    </RouterView>
  </div>
</template>

<style>
  .page-enter-active {
    transition: 600ms ease all;
  }

  .page-enter-from {
    opacity: 0;
  }
</style>