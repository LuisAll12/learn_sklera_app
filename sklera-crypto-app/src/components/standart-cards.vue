<script setup>    
import { ref, watch} from 'vue'
import CoinCard from './CoinCard.vue'

    const coinData = ref(null)

    const { coins, currency } = defineProps({
        coins: Array,
        currency: String
    })

    async function loadCoinData() {
        if (!coins || coins.length === 0 || !currency) return

        try {
            const ids = coins.join(',')
            const response = await fetch(
                `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${currency}&ids=${ids}`
            )
            const data = await response.json()
            coinData.value = data
            console.log('Coin data:', coinData.value)
        } catch (error) {
            console.error('Fehler beim Laden:', error)
        }
    }

        // Props beobachten → Daten laden
    watch(
        () => [coins, currency],
        ([newCoins, newCurrency]) => {
            if (newCoins?.length && newCurrency) {
                loadCoinData()
            }
        },
        { immediate: true }
    )
</script>

<template>
    <CoinCard v-for="coin in coinData" :key="coin.id" :coin="coin" :currency="currency" />
</template>

<style scoped>

</style>