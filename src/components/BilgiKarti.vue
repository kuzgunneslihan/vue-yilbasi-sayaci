<script setup lang="ts">
// Eğer dışarıdan bir veri alacaksan props tanımlayabilirsin
defineProps<{
  mesaj: string;
}>();
</script>

<template>
  <div class="bilgi-karti">
    <p>{{ mesaj }}</p>
  </div>
</template>

<style scoped>
.bilgi-karti {
  background-color: #ffffff;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  margin-bottom: 1rem;
  width: 100%;
  max-width: 600px;
  text-align: center;
  color: #333;
}
</style>
