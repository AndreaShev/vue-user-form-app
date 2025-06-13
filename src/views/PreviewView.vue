<template>
  <div class="preview-container">
    <h1>Просмотр данных</h1>
    
    <div class="user-info">
      <h2>Пользователь</h2>
      <p><strong>Имя:</strong> {{ userData.user.fullName }}</p>
      <p><strong>Возраст:</strong> {{ userData.user.age }}</p>
    </div>
    
    <div v-if="userData.children.length > 0" class="children-info">
      <h2>Дети</h2>
      <ul>
        <li v-for="(child, index) in userData.children" :key="index">
          {{ child.name }}, {{ child.age }} лет
        </li>
      </ul>
    </div>
    
    <button @click="$router.push('/')" class="back-button">Назад</button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const userData = ref({
  user: { fullName: '', age: null },
  children: [],
});

onMounted(() => {
  const savedData = localStorage.getItem('userData');
  if (savedData) {
    userData.value = JSON.parse(savedData);
  }
});
</script>

<style scoped>
.preview-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.user-info, .children-info {
  margin-bottom: 30px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  padding: 5px 0;
  border-bottom: 1px solid #eee;
}

.back-button {
  padding: 10px 20px;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>