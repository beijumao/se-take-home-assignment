<script setup>
import Bot from './components/Bot.vue'
import { ref } from 'vue';

const botOrderMap = ref({})

function addBot() {
  const botLength = Object.keys(botOrderMap.value).length
  botOrderMap.value[botLength + 1] = true
}

function removeBot() {
  const botLength = Object.keys(botOrderMap.value).length
  botOrderMap.value[Object.keys(botOrderMap.value)[botLength - 1]] = false
}

const normalOrderList = ref([])
const vipOrderList = ref([])

const normalOrderIndex = ref(1)
const vipOrderIndex = ref(1)

function addNormalOrder() {
  normalOrderList.value.push(`NRM-${normalOrderIndex.value}`)
  normalOrderIndex.value++
}

function addVipOrder() {
  vipOrderList.value.push(`VIP-${vipOrderIndex.value}`)
  vipOrderIndex.value++
}

function shiftOrderList(type) {
  if (type === 'VIP') {
    vipOrderList.value.shift()
  } else {
    normalOrderList.value.shift()
  }
}

const completedOrderList = ref([])

function completeOrder(order) {
  completedOrderList.value.push(order)
}

function killBot(processingOrder) {
  if (processingOrder) {
    if (processingOrder.startsWith("VIP")) {
      vipOrderList.value.unshift(processingOrder)
    } else {
      normalOrderList.value.unshift(processingOrder)
    }
  }

  const botLength = Object.keys(botOrderMap.value).length
  delete botOrderMap.value[Object.keys(botOrderMap.value)[botLength - 1]]
}
</script>

<template>
  <v-layout>
    <v-main class="d-flex" width="1200">
      <div>
        <div class="mb-4 d-flex flex-wrap overflow-y-auto" style="height: 500px; min-width: 800px; max-width: 800px;">
          <Bot v-for="bot in Object.keys(botOrderMap)" :key="`bot-${bot}`" class="mr-2 mb-2" :bot-name="bot"
            :normal-order="normalOrderList" :vip-order="vipOrderList" :active="botOrderMap[bot]" @shift="shiftOrderList"
            @complete="completeOrder" @revert="killBot" />
        </div>

        <div>
          <v-btn class="mr-2" color="primary" @click="addBot">+ Bot</v-btn>
          <v-btn color="error" @click="removeBot">- Bot</v-btn>
        </div>
      </div>

      <div>
        <v-btn class="mr-2" color="primary" @click="addNormalOrder">New Normal Order</v-btn>
        <v-btn color="primary" @click="addVipOrder">New VIP Order</v-btn>

        <v-card class="my-2 px-4 py-2" variant="tonal">
          <div class="font-weight-bold">PENDING</div>
          <div v-for="vipOrder in vipOrderList" :key="vipOrder">{{ vipOrder }}</div>
          <div v-for="normalOrder in normalOrderList" :key="normalOrder">{{ normalOrder }}</div>
        </v-card>
        <v-card class="px-4 py-2" variant="tonal" color="success">
          <div class="font-weight-bold">COMPLETE</div>
          <div v-for="order in completedOrderList" :key="order">{{ order }}</div>
        </v-card>
      </div>
    </v-main>
  </v-layout>
</template>

<style scoped></style>
