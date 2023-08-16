<script setup lang="ts">
defineProps(["orderList"]);
</script>

<template>
  <div class="pending-container">
    <h2>Pending</h2>
    <div class="pending-list">
      <span
        v-for="order in orderList"
        :key="order.id"
        :class="{ 'is-vip': order.isVip }"
      >
        {{ order.id }}
      </span>
    </div>
  </div>
</template>

<style scoped>
.pending-container {
  padding: 24px 32px;
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 50%;
}

.pending-list {
  overflow: auto;
  display: flex;
  flex-direction: column;
}

.is-vip {
  background-color: #aec6cf;
}
</style>
