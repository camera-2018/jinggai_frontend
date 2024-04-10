<script setup>
import { computed } from 'vue';

const logoUrl = computed(() => {
  return `layout/images/jinggai_dark.svg`;
});
</script>

<template>
  <div class="layout-footer">
    <img :src="logoUrl" alt="Logo" height="100" class="mr-2" />
    by
    <span class="font-medium ml-2">安盖通 - 井盖隐患智能识别系统</span>
  </div>
</template>
<style lang="scss" scoped></style>
