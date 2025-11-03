<script setup>
import { ref } from 'vue';
import { useRoute } from 'vue-router';
import { useAuth } from '../composables/auth';
const auth = useAuth();
const route = useRoute();
const arduinoId = route.params.id;

const token = auth.getToken();
console.log(`Token recuperato: ${token}`);

fetch(`https://ard24lguerrini.pythonanywhere.com/api/arduinos/${arduinoId}/dati/`, {
    method: "GET",
    headers: {
        "Authorization": `Bearer ${token}`,
        "Content-Type": "application/json",
    }
})
.then(response => {
    if (!response.ok) {
        console.error('Network response was not ok', response.statusText);
        throw new Error('Network response was not ok');
    }
    return response.json();
})
.then(data => {
    console.log('Dati ricevuti:', data);
})
.catch(error => {
    console.error('Errore durante il recupero dei dati:', error);
});

</script>
<template>
  <h1>Dati View</h1>

</template>
