<template>
  <div class=" flex flex-col flex-1 items-center">
    <!-- banner -->
    <div v-if="route.query.preview" class=" text-white p-4 bg-weather-secondary w-full text-center">
      您当前正在预览此城市，请单击“+”图标开始跟踪此城市。
    </div>
    <!-- 天气描述 -->
    <div class=" flex flex-col items-center text-white py-12">
      <h1 class=" text-4xl mb-2">{{route.params.city}}</h1>
      <p class=" text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString(
            "cn",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString(
            "cn",
            {
              timeStyle: "short",
            }
          )
        }}
      </p>
      <p class=" text-8xl mb-8">
        {{Math.round(weatherData.current.temp)}}&deg
      </p>
      <p>
        体感温度
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img class="w-[150px] h-auto" :src="
          `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
        " alt="" />
    </div>

    <hr class=" border-white border-opacity-10 border w-full" />
    <!-- 每小时天气 -->
    <div class=" max-w-screen-md w-full py-12">
      <div class=" mx-8 text-white">
        <h2 class=" mb-4">每小时天气</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div v-for="(item) in weatherData.hourly" :key="item.dt" class=" flex flex-col gap-4 items-center">
            <p class=" whitespace-nowrap text-sm">
              {{new Date(item.currentTime).toLocaleDateString('cn',{
                  hour:'numeric'
                })}}
            </p>
            <img class="w-auto h-[50px] object-cover" :src="
                `http://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`
              " alt="" />
            <p class="text-xl">
              {{ Math.round(item.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class=" border-white border-opacity-10 border w-full" />

    <!-- 一周天气 -->
    <div class=" max-w-screen-md w-full py-12">
      <div class=" mx-8 text-white">
        <h2 class=" mb-4"> 七天天气预测</h2>
        <div v-for="(item) in weatherData.daily" :key="item.dt" class=" flex items-center">
          <p class="flex-1">
            {{
              new Date(item.dt * 1000).toLocaleDateString('cn',{
                weekday:'long'
              })
            }}
          </p>
          <img class="w-[50px] h-[50px] object-cover" :src="
              `http://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`
            " alt="" />
          <div class="flex gap-2 flex-1 justify-end">
            <p>最高温度:{{Math.round(item.temp.max)}}</p>
            <p>最低温度:{{Math.round(item.temp.min)}}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- 在本地删除此城市 -->
    <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity">
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
  import axios from 'axios';
  import {
    useRoute,
    useRouter
  } from 'vue-router';

  const weatherkey = '7efa332cf48aeb9d2d391a51027f1a71'
  // axios
  const route = useRoute()
  // 获取天气数据
  const getweatherData = async () => {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${weatherkey}&units=imperial`
      )

      // 以下的代码是不同的时区展示不同的时间
      // 处理时区偏移量
      // cal current date & time
      const localOffset = new Date().getTimezoneOffset() * 60000;
      const utc = weatherData.data.current.dt * 1000 + localOffset;
      weatherData.data.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;

      // cal hourly weather offset
      weatherData.data.hourly.forEach((hour) => {
        const utc = hour.dt * 1000 + localOffset;
        hour.currentTime =
          utc + 1000 * weatherData.data.timezone_offset;
      });
      return weatherData.data

    } catch (err) {
      console.log(err);
    }
  }
  const weatherData = await getweatherData()
  console.log(weatherData);
  const router = useRouter()

  // 删除城市
  const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'))
    const updatedCities = cities.filter(item => item.id !== route.query.id)
    localStorage.setItem('savedCities', JSON.stringify(updatedCities))
    router.push({
      name: 'home'
    })
  }
</script>