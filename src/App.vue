<script setup lang="ts">
import Pending from "./components/Pending.vue";
import Completed from "./components/Completed.vue";
import { Ref, ref, watch } from "vue";

interface IOrderItem {
  id: number;
  status: "pending" | "processing" | "completed";
  isVip: boolean;
}

interface IBot {
  id: number;
  orderId: number | null;
  timer: number | null;
  interval: ReturnType<typeof setInterval> | null;
}

const orders: Ref<IOrderItem[]> = ref([]);
const bots: Ref<IBot[]> = ref([]);

function addPendingOrder(isVip: boolean) {
  const order: IOrderItem = {
    id: orders.value.length + 1,
    status: "pending",
    isVip,
  };
  const lastVip = orders.value.findLastIndex((order) => order.isVip);
  if (order.isVip) {
    lastVip === -1
      ? [orders.value.splice(0, 0, order)]
      : orders.value.splice(lastVip + 1, 0, order);
  } else {
    orders.value.push(order);
  }
}

function addBot() {
  bots.value.push({
    id: bots.value.length + 1,
    orderId: null,
    timer: null,
    interval: null,
  });
  orderMatching();
}

function removeBot() {
  const removedBot = bots.value.pop();
  if (removedBot?.orderId) {
    const order = orders.value.find((order) => order.id === removedBot.orderId);
    if (order) {
      order.status = "pending";
      clearInterval(removedBot.interval!);
    }
  }
}

function startCook(bot: IBot, order: IOrderItem) {
  bot.orderId = order.id;
  order.status = "processing";
  bot.timer = 10;
  bot.interval = setInterval(() => {
    if (bot.timer! <= 0) {
      clearInterval(bot.interval!);
      bot.orderId = null;
      bot.timer = null;
      bot.interval = null;
      order.status = "completed";
    } else {
      bot.timer! -= 1;
    }
    // console.log(toRaw(bot), toRaw(order));
  }, 1000);
}

function orderMatching() {
  const pendingOrder = orders.value.find((order) => order.status === "pending");
  const freeBot = bots.value.find((bot) => bot.orderId === null);
  if (pendingOrder && freeBot) {
    startCook(freeBot, pendingOrder);
  }
}

watch(orders, orderMatching, { deep: true });
</script>

<template>
  <div class="queue-container">
    <Pending
      :orderList="
        orders.filter(
          (order) => order.status === 'pending' || order.status === 'processing'
        )
      "
    />
    <Completed
      :orderList="orders.filter((order) => order.status === 'completed')"
    />
  </div>
  <div class="bots-container">
    <div
      class="bot"
      v-for="bot in bots"
      :key="bot.id"
      :class="{ 'bot-processing': bot.orderId, 'bot-idle': !bot.orderId }"
    >
      <span>Bot: {{ bot.id }}</span>
      <span>Order: {{ bot.orderId }}</span>
      <span>Time Left: {{ bot.timer }}</span>
    </div>
  </div>
  <div class="button-container">
    <button @click="addPendingOrder(false)">New Normal Order</button>
    <button @click="addPendingOrder(true)">New VIP Order</button>
    <button @click="addBot()">+ Bot</button>
    <button @click="removeBot()">- Bot</button>
  </div>
</template>

<style scoped>
.queue-container {
  display: flex;
  height: 50%;
  width: 100%;
}
.bots-container {
  display: flex;
  padding: 24px 32px;
  gap: 16px;
  height: 50%;
  width: 100%;
  border: 1px solid #ccc;
  flex-wrap: wrap;
  overflow: auto;
}

.bot {
  width: 140px;
  height: 140px;
  border: solid 1px #ccc;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 12px;
}

.bot-processing {
  background-color: #ff6961;
}

.bot-idle {
  background-color: #77dd77;
}

.button-container {
  display: flex;
  gap: 8px;
  position: absolute;
  right: 32px;
  bottom: 24px;
}

.is-vip {
  background-color: #aec6cf;
}
</style>
