<template>
  <div class="container">
  <FloatLabel>
    <InputText id="username" v-model="value" @keydown="onKeydown" :readonly="isReadOnly" @blur="handleBlur"/>
    <label for="username">https://</label>
    <div v-if="title">
      <p>Заголовок страницы: {{ title }}</p>
    </div>
</FloatLabel>
<Button icon="pi pi-pencil" v-if="title" @click="handleButtonClick"></Button>
</div>
</template>

<script setup>
import { ref } from 'vue';
import InputText from 'primevue/inputtext';
import FloatLabel from 'primevue/floatlabel';
import 'primeicons/primeicons.css';
import Button from 'primevue/button';
const value = ref(''); // Определяем реактивную переменную для ввода
const title = ref(''); // Создаем реактивную переменную для заголовка
const isReadOnly = ref(false);

async function getPageTitle(url) {
    try {
        const response = await fetch(url);
        
        // Проверяем, успешно ли выполнен запрос
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const htmlString = await response.text();
        const parser = new DOMParser();
        const doc = parser.parseFromString(htmlString, 'text/html');

        // Извлекаем заголовок
        const titleElement = doc.querySelector('title');
        const pageTitle = titleElement ? titleElement.innerText : 'Заголовок не найден';
        
        return pageTitle;
    } catch (error) {
        console.error('Ошибка:', error);
        return null;
    }
}

// Обработчик события keydown
async function onKeydown(event) {
    if (event.key === 'Enter') {
        if (value.value) {
            console.log('Fetching title for:', value.value);
            const pageTitle = await getPageTitle(value.value);
            console.log('Fetched title:', pageTitle);
            title.value = pageTitle;
            isReadOnly.value = true;
        }
    }
}
async function handleBlur() {
  isReadOnly.value = false;
    }
function handleButtonClick() {
  isReadOnly.value = false;
}
</script>

<style>
.container {
margin-left: 500px;
margin-top: 500px;
}
</style>
