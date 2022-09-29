<template>
  <div class=" justify-between flex py-6 px-3 bg-weather-secondary rounded-md shadow-md ">
    <div class=" flex flex-col flex-1 gap-2">
      <AnimatedPlaceholder class=" w-[50%]"></AnimatedPlaceholder>
      <AnimatedPlaceholder class=" w-[40%]"></AnimatedPlaceholder>
    </div>
    <div class="flex flex-col items-end flex-1 gap-2">
      <AnimatedPlaceholder class=" w-[50px]"></AnimatedPlaceholder>
      <AnimatedPlaceholder class=" w-[75px]"></AnimatedPlaceholder>
    </div>
  </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';

</script>