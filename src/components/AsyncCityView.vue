<template>
  <div class="flex flex-col flex-1 items-center">
    <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
    <p>You are currently previewing this city, click the '+' icon to start tracking this city</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute } from "vue-router";
const route = useRoute();

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      // `https://api.openweathermap.org/data/3.0/onecall?lat={route.query.lat}&lon={route.query.lng}&exclude={part}&appid=f959c39c40831b8a2bf897afbe082f0b&units=imperial`
    );

    // calculate current date and time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // calculate hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });
    return weatherData;
  } catch (err) {
    console.log(err);
  }
};

const weatherData = await getWeatherData();
console.log(weatherData);
</script>
