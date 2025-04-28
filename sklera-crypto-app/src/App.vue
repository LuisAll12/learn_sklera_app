<script setup>
import configChart from './components/config-chart.vue'
import fullMarket from './components/full-market.vue'
import standartCards from './components/standart-cards.vue'

import { ref, onMounted } from 'vue'

const coins = ref(["bitcoin", "ethereum", "solana", "polkadot"])
const config = ref({})
const currency = ref('eur')
const refreshInterval = ref(30)
const chartCoin = ref('bitcoin')
const show_dark_mode = ref(true)


onMounted(() => {
  const waitForSklera = () =>
    new Promise((resolve) => {
      if (window.skleraSDK) {
        window.skleraSDK.loaded((data, skleraConfig) => {
          console.log('Sklera config:', skleraConfig)
          resolve(skleraConfig)
        })
      } else {
        resolve({})
      }
    })

  waitForSklera().then((skleraConfig) => {


    const isDark = skleraConfig.show_dark_mode === true || 'dark'

    document.documentElement.setAttribute('data-theme', isDark ? 'dark' : 'light')

    // Optional: speichern
    localStorage.setItem('theme', isDark ? 'dark' : 'light')

    config.value = skleraConfig
    console.log('Sklera config:', config.value)
    coins.value = skleraConfig.coins || ['bitcoin', 'ethereum']
    currency.value = skleraConfig.currency || 'eur'
    refreshInterval.value = skleraConfig.refreshInterval || 30
    chartCoin.value = skleraConfig.chartCoin || 'bitcoin'
  })
})
</script>


<template>
  <div class="dashboard">
    <div class="left-side">
      <!-- Coin-Karten -->
      <section class="coin-cards">
        <standart-cards :coins="coins" :currency="currency" />
      </section>

      <!-- Haupt-Chart -->
      <section class="main-chart">
        <config-chart :coin="chartCoin" :currency="currency" />
      </section>
    </div>

    <aside class="market-sidebar">
      <full-market />
    </aside>
  </div>
</template>


<style scoped>
@import './style.css';
.dashboard {
  display: flex;
  flex-direction: row; 
  gap: 2rem;
  padding: 2rem;
  height: calc(100vh - 4rem);
  background-color: #0d0d0d;
  color: white;
  font-family: 'Segoe UI', sans-serif;
}


.coin-cards {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: stretch;
  flex: 0 0 auto; /* Don't grow/shrink */
}


.left-side {
  display: flex;
  flex-direction: column;
  flex: 2;
  gap: 2rem;
  overflow: hidden; /* Important to control overflow nicely */
}

.market-sidebar {
  flex: 1; /* nimmt 1 Teil vom Platz */
  background-color: #161616;
  border-radius: 1rem;
  max-height: 100%;
  overflow-y: auto;
}

.main-chart {
  flex: 1 1 auto; /* Grow to fill remaining space! */
  min-height: 0;  /* Important so flexbox allows shrinking */
  display: flex;
  flex-direction: column;
}

.main-chart > * {
  flex: 1 1 auto; /* Allow the <config-chart> to stretch fully */
  min-height: 0;
}
</style>

