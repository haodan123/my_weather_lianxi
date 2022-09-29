<template>
  <div v-for="(item) in savedCities" :key="item.id">
    <CityCard @click="gotoCityView(item)" :city="item"></CityCard>
  </div>
  <p v-if="savedCities.length===0">
    没有保存的城市，请搜索后添加</p>
</template>

<script setup>
  import axios from 'axios';
  import {
    ref
  } from 'vue';
  import {
    useRouter
  } from 'vue-router';
  import CityCard from './CityCard.vue';

  const savedCities = ref([])
  const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
      savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

      const request = []
      savedCities.value.forEach(item => {
        request.push(
          axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${item.coords.lat}&lon=${item.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
          )
        )
      })
      const weatherData = await Promise.all(request)
      weatherData.forEach((item, index) => {
        savedCities.value[index].weather = item.data
      })
    }

  }
  await getCities()

  const router = useRouter()
  // 点击卡片去城市
  const gotoCityView = (city) => {
    console.log(city);
    router.push({
      name: 'cityview',
      params: {
        state: city.state,
        city: city.city
      },
      query: {
        id: city.id,
        lat: city.coords.lat,
        lng: city.coords.lng,
      }
    })
  }
</script>

<style lang='less' scoped>

</style>