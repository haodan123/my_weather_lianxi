<template>
  <main class=" container text-white">
    <!-- 搜索框 -->
    <div class=" pt-4 mb-8 relative">
      <input @input="getSearchResults" v-model="searchQuery" type="text" placeholder="搜索你城市的天气" class=" 
        py-2 px-1 w-full 
        bg-transparent border-b
         focus:border-weather-secondary focus:outline-none focus:shadow-lg">

      <ul v-if="mapboxSearchResults.length>0"
        class=" absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">抱歉，发生了一些错误，请重试</p>
        <p v-if="!searchError&&mapboxSearchResults.length===0"> 没有找到你的搜索，请尝试不同的地方</p>
        <template v-else>
          <li @click="previewCity(item)" class=" py-2 cursor-pointer" v-for="(item) in mapboxSearchResults"
            :key="item.id">
            {{item.place_name}}
          </li>
        </template>
      </ul>
    </div>
    <!-- 天气卡片 -->
    <div class=" flex flex-col gap-4">
      <!-- Suspense是vue3提供的内置组件 -->
      <Suspense>
        <CityList></CityList>
        <template #fallback>
          <CityCardSkeleton></CityCardSkeleton>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
  import {
    ref
  } from 'vue';
  import axios from 'axios'
  import {
    useRouter
  } from 'vue-router';
  import CityList from '../components/CityList.vue';
import CityCardSkeleton from '../components/CityCardSkeleton.vue';
  // const mapboxAPIKey = 'pk.eyJ1IjoiaGFvZGFuMTIzIiwiYSI6ImNsOGxvdGFuczA4ZTUzd3A2aHV0MGpwankifQ.6vJQkYzYp2DErSIpTHwSAw'

  const mapboxAPIKey =
    "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

  const queryTimeout = ref(null)
  const searchQuery = ref('') //搜索关键字
  const mapboxSearchResults = ref([]) //搜索列表
  const searchError = ref(null); //判断是否搜索到了
  const router = useRouter()
  //点击城市
  const previewCity = (item) => {
    console.log(item);
    // 吧字符串分割成数组，然后吧城市和省结构出来
    const [city, state] = item.place_name.split(',')
    console.log(city, state);
    router.push({
      name: 'cityview',
      params: {
        state: state.replaceAll(" ", ""),
        city: city
      },
      query: {
        // 传经纬度
        lat: item.geometry.coordinates[1],
        lng: item.geometry.coordinates[0],
        preview: true
      }
    })
  }

  // 搜索时间
  const getSearchResults = () => {
    clearTimeout(queryTimeout.value)
    queryTimeout.value = setTimeout(async () => {
      if (searchQuery.value.trim() === '') {
        searchQuery.value = ''
        mapboxSearchResults.value = []
        return
      }
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        )
        mapboxSearchResults.value = result.data.features
        console.log(mapboxSearchResults.value, result.data.features);

      } catch {
        searchError.value = true
      }
    }, 300);
  }
</script>

<style lang='less' scoped>

</style>