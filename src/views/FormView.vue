<template>
  <div class="form-container">
    <div class="header">
      <h1>Персональные данные пользователя</h1>
      <div class="theme-switcher" @click="toggleTheme">
        <span>{{ isDarkMode ? '☀️' : '🌙' }}</span>
      </div>
    </div>
    
    <div class="user-form">
      <div class="form-group">
        <label for="fullName">Имя <span class="required">*</span></label>
        <input 
          id="fullName"
          v-model.trim="user.fullName"
          type="text"
          placeholder="Введите ваше имя"
          :class="{ 'error': errors.fullName }"
          @input="clearError('fullName'); validateNameInput('fullName', user.fullName)"
        >
        <div v-if="errors.fullName" class="error-message">{{ errors.fullName }}</div>
      </div>
      
      <div class="form-group">
        <label for="age">Возраст <span class="required">*</span></label>
        <input 
          id="age"
          v-model="user.age"
          type="text"
          placeholder="Укажите ваш возраст"
          :class="{ 'error': errors.age }"
          @input="clearError('age'); validateNumberInput('age', user)"
        >
        <div v-if="errors.age" class="error-message">{{ errors.age }}</div>
      </div>
    </div>

    <div class="children-section">
      <div class="section-header">
        <h2>Дети</h2>
        <div class="counter">Заполнено: {{ validChildrenCount }}/5</div>
      </div>
      
      <div v-if="children.length === 0" class="empty-state">
        <p>Дети не добавлены</p>
        <button class="add-child-btn" @click="addChild">
          <span>+</span> Добавить ребенка
        </button>
      </div>
      
      <div v-else>
        <div 
          v-for="(child, index) in children"
          :key="child.id"
          class="child-form"
        >
          <div class="form-group">
            <label :for="`child-name-${index}`">Имя ребенка <span class="required">*</span></label>
            <input
              :id="`child-name-${index}`"
              v-model.trim="child.name"
              type="text"
              placeholder="Имя ребенка"
              :class="{ 'error': errors[`child-name-${index}`] }"
              @input="clearError(`child-name-${index}`); validateNameInput(`child-name-${index}`, child.name)"
            >
            <div v-if="errors[`child-name-${index}`]" class="error-message">
              {{ errors[`child-name-${index}`] }}
            </div>
          </div>
          
          <div class="form-group">
            <label :for="`child-age-${index}`">Возраст <span class="required">*</span></label>
            <input
              :id="`child-age-${index}`"
              v-model="child.age"
              type="text"
              placeholder="Возраст ребенка"
              :class="{ 'error': errors[`child-age-${index}`] }"
              @input="clearError(`child-age-${index}`); validateNumberInput(`child-age-${index}`, child)"
            >
            <div v-if="errors[`child-age-${index}`]" class="error-message">
              {{ errors[`child-age-${index}`] }}
            </div>
          </div>
          
          <button class="remove-btn" @click="removeChild(index)">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
              <path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/>
            </svg>
            Удалить
          </button>
        </div>
        
        <button 
          v-if="children.length < 5"
          class="add-child-btn"
          @click="addChild"
        >
          <span>+</span> Добавить ребенка
        </button>
      </div>
    </div>
    
    <div class="actions">
      <button class="save-btn" @click="validateAndSave">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
          <path d="M17 3H5c-1.11 0-2 .9-2 2v14c0 1.1.89 2 2 2h14c1.1 0 2-.9 2-2V7l-4-4zm-5 16c-1.66 0-3-1.34-3-3s1.34-3 3-3 3 1.34 3 3-1.34 3-3 3zm3-10H5V5h10v4z"/>
        </svg>
        Сохранить данные
      </button>
    </div>
    
    <div v-if="showSuccess" class="success-message">
      <p>Данные успешно сохранены!</p>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

// Тема приложения
const isDarkMode = ref(false)
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
  document.documentElement.classList.toggle('dark', isDarkMode.value)
}

// Данные пользователя
const user = reactive({
  fullName: '',
  age: ''
})

// Данные детей
const children = reactive([])
let childIdCounter = 0

// Ошибки валидации
const errors = reactive({})

// Сообщение об успешном сохранении
const showSuccess = ref(false)

// Подсчет заполненных детей (минимум 1 символ в имени)
const validChildrenCount = computed(() => {
  return children.filter(child => child.name.trim().length > 0).length
})

// Добавление ребенка
const addChild = () => {
  if (children.length < 5) {
    children.push({
      id: childIdCounter++,
      name: '',
      age: ''
    })
  }
}

// Удаление ребенка
const removeChild = (index) => {
  if (confirm('Вы уверены, что хотите удалить данные этого ребенка?')) {
    children.splice(index, 1)
  }
}

// Очистка ошибки
const clearError = (field) => {
  delete errors[field]
}

// Валидация имени (не должно содержать цифр)
const validateNameInput = (field, value) => {
  // Проверка на наличие цифр
  if (/\d/.test(value)) {
    errors[field] = 'Поле не должно содержать цифр'
    return
  }
  
  // Очищаем ошибку, если она была
  clearError(field)
}

// Валидация числового поля
const validateNumberInput = (field, context) => {
  let value = context[field === 'age' ? 'age' : 'age']
  
  // Проверка на пустое значение
  if (!value) {
    clearError(field)
    return
  }
  
  // Проверка на наличие букв
  if (/[^\d]/.test(value)) {
    errors[field] = 'Поле должно содержать только цифры'
    return
  }
  
  // Преобразование в число
  const numValue = parseInt(value)
  
  // Проверка на отрицательное значение
  if (numValue < 0) {
    errors[field] = 'Значение не может быть отрицательным'
    return
  }
  
  // Очищаем ошибку, если она была
  clearError(field)
  
  // Обновляем значение в модели
  context[field === 'age' ? 'age' : 'age'] = value
}

// Валидация формы
const validateForm = () => {
  let isValid = true
  const newErrors = {}
  
  // Валидация пользователя - ФИО
  if (!user.fullName.trim()) {
    newErrors.fullName = 'Поле Имя обязательно для заполнения'
    isValid = false
  } else if (/\d/.test(user.fullName)) {
    newErrors.fullName = 'Имя не должно содержать цифр'
    isValid = false
  }
  
  // Валидация пользователя - возраст
  if (!user.age) {
    newErrors.age = 'Поле возраста обязательно для заполнения'
    isValid = false
  } else if (/[^\d]/.test(user.age)) {
    newErrors.age = 'Возраст должен содержать только цифры'
    isValid = false
  } else if (parseInt(user.age) < 0) {
    newErrors.age = 'Возраст не может быть отрицательным'
    isValid = false
  }
  
  // Валидация детей
  children.forEach((child, index) => {
    const nameField = `child-name-${index}`
    const ageField = `child-age-${index}`
    
    if (!child.name.trim()) {
      newErrors[nameField] = 'Введите имя ребенка'
      isValid = false
    } else if (/\d/.test(child.name)) {
      newErrors[nameField] = 'Имя не должно содержать цифр'
      isValid = false
    }
    
    if (!child.age) {
      newErrors[ageField] = 'Введите возраст ребенка'
      isValid = false
    } else if (/[^\d]/.test(child.age)) {
      newErrors[ageField] = 'Возраст должен содержать только цифры'
      isValid = false
    } else if (parseInt(child.age) < 0) {
      newErrors[ageField] = 'Возраст не может быть отрицательным'
      isValid = false
    }
  })
  
  // Обновляем объект ошибок
  Object.keys(errors).forEach(key => delete errors[key])
  Object.assign(errors, newErrors)
  
  return isValid
}

// Сохранение данных
const saveData = () => {
  const data = {
    user: { 
      fullName: user.fullName,
      age: parseInt(user.age)
    },
    children: children.map(child => ({ 
      name: child.name,
      age: parseInt(child.age)
    }))
  }
  
  localStorage.setItem('userData', JSON.stringify(data))
  
  // Показываем сообщение об успехе
  showSuccess.value = true
  setTimeout(() => {
    showSuccess.value = false
    router.push('/preview')
  }, 1500)
}

// Валидация и сохранение
const validateAndSave = () => {
  if (validateForm()) {
    saveData()
  }
}

// Загрузка данных при монтировании (если есть сохраненные)
onMounted(() => {
  const savedData = localStorage.getItem('userData')
  if (savedData) {
    const parsedData = JSON.parse(savedData)
    user.fullName = parsedData.user.fullName
    user.age = parsedData.user.age.toString()
    
    if (parsedData.children && parsedData.children.length) {
      parsedData.children.forEach(child => {
        children.push({
          id: childIdCounter++,
          name: child.name,
          age: child.age.toString()
        })
      })
    }
  }
})
</script>

<style scoped>
.form-container {
  max-width: 700px;
  margin: 0 auto;
  padding: 30px;
  background-color: #fff;
  border-radius: 12px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08);
}

.dark .form-container {
  background-color: #2c3e50;
  color: #ecf0f1;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.3);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  padding-bottom: 15px;
  border-bottom: 1px solid #eee;
}

.dark .header {
  border-bottom: 1px solid #34495e;
}

.theme-switcher {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #f5f7fa;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 20px;
  transition: transform 0.3s, background-color 0.3s;
}

.theme-switcher:hover {
  transform: scale(1.1);
}

.dark .theme-switcher {
  background-color: #34495e;
}

.user-form {
  display: flex;
  /* grid-template-columns: 1fr 1fr; */
  gap: 20px;
  margin-bottom: 40px;
  justify-content: space-around;
  overflow: hidden;
}

@media (max-width: 600px) {
  .user-form {
     flex-direction: column;
  }
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
  color: #2c3e50;
}

.dark label {
  color: #ecf0f1;
}

.required {
  color: #e74c3c;
}

input {
  width: 100%;
  padding: 12px 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.3s;
}

.dark input {
  background-color: #34495e;
  border-color: #2c3e50;
  color: #ecf0f1;
}

input:focus {
  border-color: #3498db;
  outline: none;
  box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.2);
}

input.error {
  border-color: #e74c3c;
}

.error-message {
  margin-top: 6px;
  color: #e74c3c;
  font-size: 14px;
}

.children-section {
  margin-bottom: 40px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.counter {
  background-color: #3498db;
  color: white;
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 14px;
}

.empty-state {
  text-align: center;
  padding: 30px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  margin-bottom: 20px;
}

.dark .empty-state {
  border-color: #34495e;
}

.empty-state p {
  margin-bottom: 20px;
  color: #7f8c8d;
}

.child-form {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
  position: relative;
  display: flex;
  /* grid-template-columns: 1fr 1fr auto; */
  gap: 15px;
  align-items: center;
  justify-content: space-between;
}

.dark .child-form {
  background-color: #34495e;
}

@media (max-width: 768px) {
  .child-form {
    flex-direction: column;
  }
}

.remove-btn {
  background-color: #e74c3c;
  color: white;
  border: none;
  border-radius: 6px;
  padding: 12px 15px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: background-color 0.3s;
  height: 60px;
  white-space: nowrap;
}

.remove-btn svg {
  width: 18px;
  height: 18px;
  fill: white;
}

.remove-btn:hover {
  background-color: #c0392b;
}

.add-child-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  background-color: #2ecc71;
  color: white;
  border: none;
  border-radius: 8px;
  padding: 12px 20px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 500;
  transition: background-color 0.3s;
  width: 100%;
}

.add-child-btn:hover {
  background-color: #27ae60;
}

.add-child-btn span {
  font-size: 24px;
  line-height: 1;
}

.actions {
  text-align: center;
}

.save-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 8px;
  padding: 14px 30px;
  font-size: 18px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.3s;
  width: 100%;
  max-width: 350px;
  margin: 0 auto;
}

.save-btn:hover {
  background-color: #2980b9;
}

.save-btn svg {
  width: 20px;
  height: 20px;
  fill: white;
}

.success-message {
  margin-top: 20px;
  padding: 15px;
  background-color: #2ecc71;
  color: white;
  border-radius: 8px;
  text-align: center;
  animation: fadeIn 0.5s;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>