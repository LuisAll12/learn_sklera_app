<script setup>
import { ref, onMounted } from 'vue';

const weather = ref(null);
const location = ref('Luzern');
const unit = ref('metric');

onMounted(() => {
  const waitForSklera = () =>
    new Promise((resolve) => {
      if (window.skleraSDK) {
        window.skleraSDK.loaded((data, config) => {
          console.log('Sklera config:', config);
          resolve(config);
        });
      } else {
        resolve({});
      }
    });

  waitForSklera().then(async (config) => {
    location.value = config.location || 'Luzern';
    unit.value = config.unit || 'metric';

    const apiKey = '55e18ad0b4d319e661f39a23b65e894f';

    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${location.value}&units=${unit.value}&appid=${apiKey}&lang=de`
    );
    weather.value = await response.json();
  });
});
</script>

<template>
  <div class="weather-app">
    <h1>Wetter in {{ location }}</h1>
    <div v-if="weather">
      <p>{{ weather.main.temp }} °{{ unit === 'metric' ? 'C' : 'F'}}</p>
      <p>{{ weather.weather[0].description }}</p>
    </div>
    <div v-else>
      <p>Lade Wetterdaten...</p>
    </div>
  </div>
</template>

<style scoped>
.weather-app {
  font-family: sans-serif;
  text-align: center;
  margin-top: 2rem;
}
</style>
