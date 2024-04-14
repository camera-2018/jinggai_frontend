<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import axios from 'axios';

const delay = ref(0);
const connected = ref(true);

const pingBackend = async () => {
  try {
    const start = Date.now();
    await axios.get('http://127.0.0.1:8000/ping');
    const end = Date.now();
    delay.value = end - start;
    connected.value = true;
  } catch (error) {
    connected.value = false;
    console.error(`"Ping failed:"`, error);
  }
};

// const intervalId = setInterval(pingBackend, 2000);

onMounted(() => {
  pingBackend();
});

// onUnmounted(() => {
//   clearInterval(intervalId);
// });
</script>

<template>
  <div>
    <Button v-if="connected" icon="pi pi-check" severity="success" rounded outlined aria-label="success" v-tooltip.bottom="`${delay}ms`" />
    <Button v-else severity="danger" icon="pi pi-times" rounded outlined aria-label="fail" v-tooltip.bottom="`连接后端失败`" />
  </div>
</template>

<style>
.light {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: inline-block;
  margin-right: 10px;
}
</style>
