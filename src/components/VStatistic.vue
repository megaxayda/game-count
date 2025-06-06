<template>
  <div class="v-statistic">
    <div class="v-statistic__title" :class="`text--${color}`">{{ title }}</div>
    <div class="v-statistic__value" :class="`${color}--text`">{{ value }}</div>
  </div>
</template>

<script setup>
defineProps({
  title: {
    type: String,
    required: true,
  },
  value: {
    type: [String, Number],
    required: true,
  },
  color: {
    type: String,
    default: "primary",
  },
});
</script>

<style scoped>
.v-statistic {
  text-align: center;
  padding: 12px;
  transition: all 0.3s ease;
}

.v-statistic:hover {
  transform: translateY(-2px);
}

.v-statistic__title {
  font-size: 0.875rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 8px;
  opacity: 0.8;
}

.v-statistic__value {
  font-size: 2rem;
  font-weight: 700;
}
</style>
