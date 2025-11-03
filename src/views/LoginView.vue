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
    console.log(data);
    arduinos.value = data;
})

const isViewDataButtonDisabled = () => {
  return selectedArduino.value === null;
};

const onViewDataClick = (e) => {
  console.log(`Arduino selezionato: ${selectedArduino.value}`);
  const macaddress = arduinos.value.
    find(arduino => arduino.id === selectedArduino.value).macaddress;
  console.log(`MAC Address: ${macaddress}`);
  //effettua login
  
  fetch(`https://ard24lguerrini.pythonanywhere.com/api/auths`, {
      method: "POST",
      headers: {
          "Content-Type": "application/json",
      },
      body: JSON.stringify({
          "macaddress": macaddress
      }),
  })
  .then(response => {
    if (!response.ok) {
        alert('Login failed: ' + response.statusText);
        throw new Error('Login failed');
    }
    return response.json();
  })
  .then(data => {
      console.log(data);
      //naviga alla view dati
      //router.push({ name: 'dati', params: { macaddress: macaddress } });
  });
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
  <button @click="onViewDataClick"
    :disabled="isViewDataButtonDisabled()">
    Vai ai dati
  </button>
</template>
