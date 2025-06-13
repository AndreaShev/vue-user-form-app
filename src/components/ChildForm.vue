<template>
  <div class="child-form">
    <input 
      v-model="child.name"
      type="text"
      placeholder="Имя ребенка"
      required
    >
    <input
      v-model.number="child.age"
      type="number"
      min="0"
      placeholder="Возраст"
      required
    >
    <button @click="$emit('remove')">Удалить</button>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

defineProps({
  child: {
    type: Object,
    required: true,
  }
});
</script>

<style scoped>
.child-form {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}
</style>