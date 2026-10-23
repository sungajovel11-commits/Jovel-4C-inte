<template>
  <div class="mx-auto d-flex justify-center align-center" style="height: 90vh;">
    <v-card width="450" rounded="xl" class="pa-5 text-center bg-card" elevation="2">
      <v-card-title class="text-h5 font-weight-bold">Weather App</v-card-title>
      <v-card-subtitle class="mb-4">Central Luzon, Philippines</v-card-subtitle>
      
      <v-card-text>
        <v-icon size="80" color="primary" class="my-2">mdi-weather-partly-cloudy</v-icon>
        
        <div v-if="weather" class="my-4">
          <h1 class="text-h3 font-weight-bold">{{ weather.temperature }}°C</h1>
          <p class="text-subtitle-1 text-grey-darken-1 mt-2">Current Weather</p>
          
          <v-row class="mt-4 text-center">
            <v-col cols="6">
              <v-icon color="grey">mdi-water-percent</v-icon>
              <div class="text-body-2">Humidity: 76%</div>
            </v-col>
            <v-col cols="6">
              <v-icon color="grey">mdi-weather-windy</v-icon>
              <div class="text-body-2">Wind: 10 mph</div>
            </v-col>
          </v-row>
        </div>

        <div v-else class="my-4">
          <v-progress-circular indeterminate color="primary"></v-progress-circular>
          <p class="text-body-2 mt-2">Fetching weather data...</p>
        </div>

        <v-btn color="primary" class="mt-4" block rounded="lg" @click="fetchWeather">Refresh Weather</v-btn>
      </v-card-text>
    </v-card>
  </div>
</template>

<script lang="ts" setup>
//@ts-nocheck
definePageMeta({
  layout: 'default'
})

const weather = ref(null)

const fetchWeather = async () => {
  try {
    const res = await $fetch('https://api.open-meteo.com/v1/forecast?latitude=15.05&longitude=120.68&current_weather=true')
    weather.value = res.current_weather
  } catch (error) {
    console.error('Error fetching weather data:', error)
  }
}

onMounted(() => {
  fetchWeather()
})
</script>

<style scoped>
.bg-card {
  background: #FFFFFF;
}
</style>