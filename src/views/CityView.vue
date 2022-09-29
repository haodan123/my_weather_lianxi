<template>
  <div>
    <!-- Suspense是vue3官网内置的组件，用来在组件树中协调对异步依赖的处理-->
    <Suspense>
      <AsyncCityView></AsyncCityView>
      <template #fallback>
        <CityViewSkeleton/>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
  import AsyncCityView from "../components/AsyncCityView.vue";
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
</script>

<style lang='less' scoped>

</style>