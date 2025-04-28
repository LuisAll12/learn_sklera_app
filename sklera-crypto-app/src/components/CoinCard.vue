<template>
    <div class="coin-card" v-if="coin">
        <div class="top-right">
            <ClockIcon class="top-icon" />
            <span class="top-text">Last 24h</span>
        </div>
        <div class="coin-header">
            <img :src="coin.image" class="coin-icon" />
            <div>
                <h3 class="coin-title">
                    {{ coin.name }}
                    <span class="symbol">({{ coin.symbol.toUpperCase() }})</span>
                </h3>
                <!-- <p class="subtext">Proof of Stake</p> -->
            </div>
        </div>

        <div class="reward-section">
            <p class="label">Reward Rate</p>
            <p class="reward-value">
                {{ coin.current_price.toLocaleString("de-DE", {
                style: "currency",
                currency: currency,
                }) }}
                <span v-if="coin.price_change_percentage_24h > 0"class="change-positive">▲ +{{ coin.price_change_percentage_24h.toFixed(2) }}%</span>
                <span v-else-if="coin.price_change_percentage_24h < 0" class="change-negative">▼ {{ coin.price_change_percentage_24h.toFixed(2) }}%</span>
                <span v-else-if="coin.price_change_percentage_24h == 0" class="change-neutral">• 0.00%</span>
            </p>
        </div>
    </div>
    <div v-else>
        <p class="loading">Loading...</p>
    </div>
</template>

<script setup>
import { ClockIcon } from '@heroicons/vue/24/outline'
defineProps({
    coin: Object,
    currency: String,
});
</script>

<style scoped>
.coin-card {
    position: relative; /* Needed for .top-right to be positioned inside */
    background: radial-gradient(circle at bottom, #1c1c1c, #151515 80%);
    color: white;
    border-radius: 1rem;
    padding: 1rem;
    width: auto;
    min-width: 230px;
    box-shadow: 0 0 12px rgba(0, 0, 0, 0.3);
    font-family: "Segoe UI", sans-serif;
    margin: 10px;
}
@media (max-width: 1024px) {
    .coin-card {
        width: 100%;
        min-width: 0; 
        margin: 10px;
    }
}

.coin-header {
    display: flex;
    align-items: center;
    gap: 0.8rem;
}

.coin-icon {
    width: 32px;
    height: 32px;
}

.coin-title {
    font-size: 1rem;
    font-weight: 600;
    margin: 0;
}

.symbol {
    color: #999;
    font-weight: 400;
}

.subtext {
    font-size: 0.75rem;
    color: #888;
    margin-top: 0.2rem;
}

.reward-section {
    margin-top: 1rem;
}

.label {
    font-size: 0.8rem;
    color: #888;
    margin-bottom: 0.2rem;
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

.top-right {
    position: absolute;
    top: 1rem;
    right: 1rem;
    display: flex;
    align-items: center;
    gap: 0.3rem;
    font-size: 0.75rem;
    color: #aaa;
}

.top-icon {
    width: 16px;
    height: 16px;
    opacity: 0.7;
}

.top-text {
    font-size: 0.75rem;
    font-weight: 500;
}

</style>
