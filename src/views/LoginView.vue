<script setup>
import { useRouter } from 'vue-router';

const router = useRouter();

const onViewDataClick = (e) => {
  router.push('/dati');
};
</script>

<template>
  <h1>Login View</h1>
  <button @click="onViewDataClick">Vai ai dati</button>
</template>
