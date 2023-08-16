<script setup lang="ts">
defineProps(["orderList"]);
</script>

<template>
  <div class="completed-container">
    <h2>Completed</h2>
    <div class="completed-list">
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
.completed-container {
  overflow: auto;
  padding: 24px 32px;
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 50%;
  border-left: 1px solid #ccc;
}

.completed-list {
  overflow: auto;
  display: flex;
  flex-direction: column;
}

.is-vip {
  background-color: #aec6cf;
}
</style>
