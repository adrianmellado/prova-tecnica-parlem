<script setup>
  import { ref, onMounted } from 'vue';
  const clients = ref([]); // Estat reactiu

  onMounted(() => {
    fetch('http://localhost:3000/client') // Realitzar la sol·licitud GET per obtenir les dades al servidor
      .then(response => response.json()) // Parsejar la resposta com a JSON
      .then(data => clients.value = data) // Ara 'clients.value' conté les dades del fitxer JSON
      .catch(error => console.error(error.message));
  });
</script>

<template>
  <h1>Clients</h1>
  <div v-if="clients.length">
    <div v-for="client in clients" :key="client.id" class="client">
      <h2>{{ client.givenName }}</h2>
    </div>
  </div>
  <div v-else>
    <p>No hi ha clients disponibles.</p>
  </div>
</template>

<style scoped>

</style>