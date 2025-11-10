<script setup>
import { ref } from 'vue';
import { useRoute } from 'vue-router';
import { useAuth } from '../composables/auth';
import { Line } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip,PointElement, LineElement, Legend, CategoryScale, LinearScale } from 'chart.js'


const arduinoDetails = ref({});
const datiRx = ref([]);
const datiByTipo = ref(null);
const auth = useAuth();
const route = useRoute();
const arduinoId = route.params.id;
const token = auth.getToken();
console.log(`Token recuperato: ${token}`);
const chartData=ref({
        labels: [  ],
        datasets: [ { data: [] } ]
      });
const chartOptions=ref({ responsive: true});
ChartJS.register(Title, Tooltip, Legend, PointElement,LineElement, CategoryScale, LinearScale);

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
    datiRx.value = data;
    chartData.value = {
        labels: data.map(v => new Date(v.data_ora).toLocaleString()),
        datasets: [ { label: "LUCE",data:data.map(v => v.valore) } ]
      }
    chartOptions.value = {
        responsive: true
    }
    
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
  <div>
    <h2>Arduino Selezionato:</h2>
    <p>ID: {{ arduinoDetails.id }}</p>
    <p>Nome: {{ arduinoDetails.nome }}</p>
    <p>MAC Address: {{ arduinoDetails.macaddress }}</p>
  </div>

  <h2>Grafico</h2>
  <Line style="width: 100%;"
    :options="chartOptions"
    :data="chartData"
    arial-label="luce"
    aria-describedby="tb" />

  <h2>Tabella dati</h2>
  <table id="tb">
    <thead>
        <th>ID</th>
        <th>TIPO</th>
        <th>DATA</th>
        <th>LETTURA</th>
    </thead>
    <tbody>
        <tr v-for="v in datiRx">
            <td>{{ v.id }}</td>
            <td>{{ v.tipo }}</td>
            <td>{{ new Date(v.data_ora).toLocaleString() }}</td>
            <td>{{ v.valore }}</td>
        </tr>
    </tbody>
  </table>

</template>
