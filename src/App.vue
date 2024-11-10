<script setup>
import { ref } from 'vue';

const appKey = '36a8b42d2c4acc024f4cea6c7623a94d';
const query = ref('');
const urlBase = 'https://api.openweathermap.org/data/2.5/';
const weather = ref(null);
const isLoading = ref(false);
const error = ref(null);

const fetchData = async () => {
  if (query.value) {
    isLoading.value = true;
    error.value = null; // Clear previous errors if any

    try {
      const response = await fetch(
        `${urlBase}weather?q=${query.value}&units=metric&appid=${appKey}`
      );
      if (!response.ok) {
        throw new Error('Failed to fetch weather data');
      }

      const data = await response.json();
      weather.value = data;

    } catch (err) {
      error.value = err.message || 'An error occurred';
      console.log(err);
    } 
    finally {
      isLoading.value = false;
      query.value = '';
    }
  }
};
</script>

<template>
  <div class="container">
    <div class="wrapper">
      <div class="header">
        <h1>Weather App</h1>
        <img src="../public/img/icons8-weather.svg" alt="">
      </div>

      <div>
        <input  @keydown.enter="fetchData" v-model="query" type="text" placeholder="Enter your location" />
        <button @click="fetchData">Check</button>
      </div>
      
      
      <div v-if="isLoading" class="spinner-border" role="status">
        <span class="sr-only">Loading...</span>
      </div>
      
      <div v-else-if="error">{{ error }}</div>
      
      <div v-else-if="weather">
        <div class="weather-title">
          <h1>{{ weather.name }}, <span>{{ weather.sys.country }}</span></h1>
          
        </div>
        <h2>{{ Math.floor(weather.main.temp) }}°C</h2>
      </div>
    </div>
  </div>
 
</template>