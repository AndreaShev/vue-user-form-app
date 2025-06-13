<template>
  <div class="form-container">
    <h1>Персональные данные</h1>
    <div class="user-form">
      <input v-model="user.fullName" type="text" placeholder="Имя" required>
      <input v-model.number="user.age" type="number" min="0" placeholder="Возраст" required>
    </div>

    <h2>Дети (макс. 5)</h2>
    <ChildForm
      v-for="(child, index) in children"
      :key="index"
      :child="child"
      @remove="removeChild(index)"
    />
    
    <button 
      v-if="children.length < 5"
      @click="addChild"
      class="add-button"
    >
      + Добавить ребенка
    </button>
    
    <button @click="saveData" class="save-button">Сохранить</button>
  </div>
</template>

<script setup>
import { reactive } from 'vue';
import { useRouter } from 'vue-router';
import ChildForm from '@/components/ChildForm.vue';

const router = useRouter();

const user = reactive({
  fullName: '',
  age: null,
});

const children = reactive([]);

function addChild() {
  if (children.length < 5) {
    children.push({ name: '', age: null });
  }
}

function removeChild(index) {
  children.splice(index, 1);
}

function saveData() {
  const data = {
    user: { ...user },
    children: [...children],
  };
  localStorage.setItem('userData', JSON.stringify(data));
  router.push('/preview');
}
</script>

<style scoped>
.form-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.user-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 30px;
}

input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-button {
  padding: 10px 20px;
  font-size: 1.2rem;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.save-button {
  display: block;
  margin-top: 20px;
  padding: 12px 25px;
  background-color: #2196F3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>