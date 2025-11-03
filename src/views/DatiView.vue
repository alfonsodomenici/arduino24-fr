<script setup>
import { ref } from 'vue';
import { useRoute } from 'vue-router';
import { useAuth } from '../composables/auth';

const arduinoDetails = ref({});
const datiByTipo = ref(null);
const auth = useAuth();
const route = useRoute();
const arduinoId = route.params.id;
const token = auth.getToken();
console.log(`Token recuperato: ${token}`);



//leggo i dati associati all'arduino
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
    datiByTipo.value = Map.groupBy(data, (d) => d.tipo);
    console.dir('Dati raggruppati per tipo:', [...datiByTipo.value.entries()]);
    console.dir('Dati di tipo temperatura:', datiByTipo.value.get('LIGHT'));
  })
.catch(error => {
    console.error('Errore durante il recupero dei dati:', error);
});

//leggo i dati dell'arduino
fetch(`https://ard24lguerrini.pythonanywhere.com/api/arduinos/${arduinoId}`, {
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
    arduinoDetails.value = data;
    console.log('Arduino ricevuto:', data);
})
.catch(error => {
    console.error('Errore durante il recupero dei dati:', error);
});

</script>
<template>
  <h1>Dati View</h1>
  <div>
    <h2>Arduino Selezionato:</h2>
    <p>ID: {{ arduinoDetails.id }}</p>
    <p>Nome: {{ arduinoDetails.nome }}</p>
    <p>MAC Address: {{ arduinoDetails.macaddress }}</p>
  </div>

</template>
