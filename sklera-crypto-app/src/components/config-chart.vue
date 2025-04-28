<script setup>
import { ref, watch } from 'vue'
import {
    Chart as ChartJS,
    LineElement,
    PointElement,
    LinearScale,
    CategoryScale
} from 'chart.js'
import { Line } from 'vue-chartjs'

ChartJS.register(LineElement, PointElement, LinearScale, CategoryScale)

// Props
const props = defineProps({
    coin: String,
    currency: String
})

const coinData = ref(null)
const chartData = ref(null)
const percentageChange = ref(0)

const loadCoinData = async () => {
    try {
        const priceRes = await fetch(
            `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${props.currency}&ids=${props.coin}`
        )
        const priceJson = await priceRes.json()
        coinData.value = priceJson[0]

        const chartRes = await fetch(
            `https://api.coingecko.com/api/v3/coins/${props.coin}/market_chart?vs_currency=${props.currency}&days=7&interval=daily`
        )
        const chartJson = await chartRes.json()
        console.log('Chart data:', chartJson)
        // Extract the prices array (each entry: [timestamp, price])
        const pricesLineC = chartJson.prices;

        // Get the first and last prices
        const firstPrice = pricesLineC[0][1];
        const lastPrice = pricesLineC[pricesLineC.length - 1][1];

        // Determine if the market gained
        const marketGained = lastPrice > firstPrice;
        percentageChange.value = ((lastPrice - firstPrice) / firstPrice) * 100;


        const prices = chartJson.prices.map(p => p[1])
        const labels = chartJson.prices.map(p =>
        new Date(p[0]).toLocaleDateString()
        )
        const linecolor = marketGained ? '#4ade80' : '#f87171'
        chartData.value = {
        labels,
        datasets: [
            {
            label: `${props.coin} Price`,
            data: prices,
            borderColor: linecolor,
            backgroundColor: 'transparent',
            tension: 0.3
            }
        ]
        }
    } catch (err) {
        console.error('Fehler beim Laden:', err)
    }
}

// Wenn coin und currency gesetzt sind, lade Daten
watch(
    () => [props.coin, props.currency],
    ([newCoin, newCurrency]) => {
        if (newCoin && newCurrency) {
            loadCoinData()
        }
    },
    { immediate: true }
)
</script>

<template>
    <div v-if="coinData" class="card">
        <div class="header">
            <img :src="coinData.image" :alt="coinData.name" width="40" />
            <h3>{{ coinData.name }} ({{ coinData.symbol.toUpperCase() }})</h3>
        </div>
        <div class="reward-section">
            <p class="label">Reward Rate</p>
            <p class="reward-value">
                {{ coinData.current_price.toLocaleString("de-DE", {
                    style: "currency",
                    currency: currency
                }) }}
                </p>
                <div>
                    <span v-if="percentageChange > 0" class="change-positive">▲ +{{ percentageChange.toFixed(2) }}%</span>
                    <span v-else-if="percentageChange < 0" class="change-negative">▼ {{ percentageChange.toFixed(2) }}%</span>
                    <span v-else class="change-neutral">• 0.00%</span>
            </div>
        </div>
        <div class="chart">
            <Line
                v-if="chartData"
                :data="chartData"
                :options="{ responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }"/>
        </div>
    </div>
    <div v-else>
        <p class="loading">Loading...</p>
    </div>
</template>

<style scoped>
    .card {
    display: flex;
    flex-direction: column;
    justify-content: space-between; /* Chart bleibt unten */
    background-color: #1a1a1a;
    color: white;
    border-radius: 1rem;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    height: 90%;
    padding: 1rem;
    }
    .chart {
    flex-grow: 1;
    height: 100%;
    position: relative;
    margin-top: 1rem;
    }
    .header {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }
    .price {
        font-size: 1.3rem;
    }
    .green {
        color: #4ade80;
    }
    .red {
        color: #f87171;
    }
    .chart {
        height: 120px;
        margin-top: 1rem;
    }
    .reward-value {
        font-size: 1.4rem;
        font-weight: bold;
    }
    .change-positive {
        color: #4ade80;
        font-size: 0.9rem;
        margin-left: 0.3rem;
    }
    .change-negative {
        color: #f87171;
        font-size: 0.9rem;
        margin-left: 0.3rem;
    }
    .change-neutral {
        color: #999;
        font-size: 0.9rem;
        margin-left: 0.3rem;
    }
</style>
