<template>
  <div class="mx-auto d-flex justify-center align-center py-6" style="min-height: 90vh;">
    <v-card width="460" rounded="xl" class="pa-5 text-center bg-card" elevation="2">
      <v-card-title class="text-h5 font-weight-bold">Weather App</v-card-title>
      <v-card-subtitle class="mb-4">{{ locationLabel }}</v-card-subtitle>

      <v-card-text>
        <v-form class="d-flex ga-2 mb-4" @submit.prevent="fetchWeather">
          <v-text-field
            v-model="searchLocation"
            label="Search location"
            placeholder="e.g. San Fernando, Pampanga"
            variant="outlined"
            density="compact"
            hide-details
            prepend-inner-icon="mdi-magnify"
          />
          <v-btn
            type="submit"
            color="primary"
            :loading="isLoading"
            rounded="lg"
          >
            Search
          </v-btn>
        </v-form>

        <v-alert
          v-if="errorMessage"
          type="error"
          variant="tonal"
          density="compact"
          class="mb-4 text-left"
        >
          {{ errorMessage }}
        </v-alert>

        <div v-if="weather" class="my-4">
          <v-icon size="80" color="primary" class="my-2">{{ weatherInfo.icon }}</v-icon>
          <h1 class="text-h3 font-weight-bold">{{ weather.temperature }}°C</h1>
          <p class="text-subtitle-1 text-grey-darken-1 mt-1">{{ weatherInfo.label }}</p>

          <v-row class="mt-4 text-center">
            <v-col cols="6">
              <v-icon color="grey">mdi-water-percent</v-icon>
              <div class="text-body-2">Humidity: {{ humidity }}%</div>
            </v-col>
            <v-col cols="6">
              <v-icon color="grey">mdi-weather-windy</v-icon>
              <div class="text-body-2">Wind: {{ windSpeed }} km/h</div>
            </v-col>
          </v-row>
        </div>

        <div v-else-if="isLoading" class="my-4">
          <v-progress-circular indeterminate color="primary"></v-progress-circular>
          <p class="text-body-2 mt-2">Fetching weather data...</p>
        </div>

        <v-btn
          color="primary"
          class="mt-4"
          block
          rounded="lg"
          :loading="isLoading"
          @click="fetchWeather"
        >
          Refresh Weather
        </v-btn>
      </v-card-text>
    </v-card>
  </div>
</template>

<script lang="ts" setup>
//@ts-nocheck
definePageMeta({
  layout: 'default'
})

const searchLocation = ref('San Fernando, Pampanga')
const locationLabel = ref('Central Luzon, Philippines')
const weather = ref(null)
const humidity = ref('--')
const windSpeed = ref('--')
const isLoading = ref(false)
const errorMessage = ref('')

const weatherInfo = computed(() => {
  const code = weather.value?.weathercode
  if (code === 0) return { label: 'Clear Sky', icon: 'mdi-weather-sunny' }
  if (code <= 3) return { label: 'Partly Cloudy', icon: 'mdi-weather-partly-cloudy' }
  if (code <= 48) return { label: 'Foggy', icon: 'mdi-weather-fog' }
  if (code <= 67) return { label: 'Rainy', icon: 'mdi-weather-rainy' }
  if (code <= 82) return { label: 'Rain Showers', icon: 'mdi-weather-pouring' }
  return { label: 'Thunderstorm', icon: 'mdi-weather-lightning-rainy' }
})

const fetchWeather = async () => {
  errorMessage.value = ''
  const rawQuery = searchLocation.value.trim()
  if (!rawQuery) {
    errorMessage.value = 'Please enter a city or municipality.'
    return
  }

  const parts = rawQuery.split(',').map((p) => p.trim()).filter(Boolean)
  const cityName = parts[0] || rawQuery
  const regionName = parts[1]?.toLowerCase() || ''

  isLoading.value = true
  try {
    const geoRes = await $fetch('https://geocoding-api.open-meteo.com/v1/search', {
      params: { name: cityName, count: 10, language: 'en', format: 'json' }
    })

    const results = geoRes?.results || []
    if (!results.length) {
      weather.value = null
      errorMessage.value = `Location "${rawQuery}" not found.`
      return
    }

    const place =
      (regionName
        ? results.find(
            (item) =>
              item.admin1?.toLowerCase().includes(regionName) ||
              item.admin2?.toLowerCase().includes(regionName) ||
              item.country?.toLowerCase().includes(regionName)
          )
        : results.find((item) => item.country_code === 'PH')) || results[0]

    const forecastRes = await $fetch('https://api.open-meteo.com/v1/forecast', {
      params: {
        latitude: place.latitude,
        longitude: place.longitude,
        current_weather: true,
        current: 'relative_humidity_2m,wind_speed_10m',
        timezone: 'auto'
      }
    })

    weather.value = forecastRes.current_weather
    humidity.value = forecastRes.current?.relative_humidity_2m ?? '--'
    windSpeed.value = forecastRes.current?.wind_speed_10m ?? forecastRes.current_weather?.windspeed ?? '--'
    locationLabel.value = [place.name, place.admin2 || place.admin1, place.country]
      .filter(Boolean)
      .join(', ')
  } catch (error) {
    console.error('Error fetching weather data:', error)
    errorMessage.value = 'Failed to fetch weather data.'
  } finally {
    isLoading.value = false
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