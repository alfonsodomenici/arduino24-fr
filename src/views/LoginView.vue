<script setup>
import { useRouter } from 'vue-router';
import { ref } from 'vue';

const arduinos =ref([]);
const selectedArduino = ref(null);
const router = useRouter();

fetch(`https://ard24lguerrini.pythonanywhere.com/api/arduinos/`, {
    method: "GET",
    headers: {
        "Content-Type": "application/json",
    }
})
.then(response => response.json())
.then(data => {
    arduinos.value = data;
})


const onViewDataClick = (e) => {
  console.log(`Arduino selezionato: ${selectedArduino.value}`);
  router.push('/dati');
};
</script>

<template>
  <h1>Login View</h1>
  <select v-model="selectedArduino">
    <option v-for="arduino in arduinos" 
      :key="arduino.id" :value="arduino.id">
      {{ arduino.nome }}
    </option>
  </select>
  <button @click="onViewDataClick">Vai ai dati</button>
</template>
