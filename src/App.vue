<script setup lang="ts">
import RedPacketRain from './components/RedPacketRain.vue'

const activityFinish = () => {
  console.log('活动结束')
}
</script>

<template>
  <RedPacketRain @done="activityFinish"/>
</template>

<style scoped>
</style>
