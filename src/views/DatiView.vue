<script setup>
import { ref } from 'vue';

const msg = ref('');

const onChangeMessage = () => {
  msg.value = `Messaggio cambiato alle ${new Date().toLocaleTimeString()}`;
};
</script>
<template>
  <h1>Dati View</h1>

  <p>Messaggio: {{ msg }}</p>

  <button @click="onChangeMessage">Cambia Messaggio</button>
</template>
