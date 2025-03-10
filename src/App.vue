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
  <div class="max-w-4xl mx-auto p-6">
    <!-- Títol -->
    <h1 class="text-3xl font-bold text-yellow-400 mb-4">
      Clients <span class="text-black">telecom</span>
    </h1>
    <div v-if="clients.length">
      <!-- Targeta client -->
      <div v-for="client in clients" :key="client._id" class="bg-white shadow-md rounded-lg p-6 mb-6 border border-gray-200">
        <div class="flex justify-between items-center">
          <div>
            <h2 class="text-xl font-semibold text-gray-800">{{ client.givenName }} {{ client.familyName1 }}</h2>
            <p class="text-gray-500">{{ client.email }}</p>
            <p class="text-gray-500">{{ client.phone }}</p>
            <p class="text-gray-400 text-sm">{{ client.docType.toUpperCase() }}: {{ client.docNum }}</p>
          </div>
        </div>

      </div>
    </div>

    <!-- Si no hi ha clients -->
    <div v-else class="text-gray-500 text-center mt-10">
      <p>No hi ha clients disponibles</p>
    </div>
  </div>
</template>

<style scoped>

</style>