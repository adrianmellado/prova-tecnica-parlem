<script setup>
  import { ref, onMounted, computed } from 'vue';
  import ProducteList from './components/ProducteList.vue'; // Importar el component Producte

  const clients = ref([]); // Llistat de clients
  const activeClient = ref(null); // Client amb productes visibles
  const loading = ref(true); // Estat de càrrega
  const searchClientQuery = ref(""); // Recerca de clients

  onMounted(async () => {
    try {
      const [clientsData, productesData] = await Promise.all([
        fetch('http://localhost:3000/client').then(res => res.json()),
        fetch('http://localhost:3000/producte').then(res => res.json())
      ]);

      // Asignar productes directament a cada client
      clients.value = clientsData.map(client => ({
        ...client,
        productes: productesData.filter(p => p.customerId === client.customerId)
      }));
      console.log();

    } catch (error) {
      console.error("Error al carregar les dades:", error.message);
    } finally {
      loading.value = false; // Ocultar indicador de càrrega
    }
  });

  // Funció per canviar la visibilitat dels productes
  const toggleProductes = (customerId) => {
    activeClient.value = activeClient.value === customerId ? null : customerId;
  };

  // Filtrar y ordenar clientes
  const filteredClients = computed(() => {
    return [...clients.value]
      .filter(client => {
        const searchTerm = searchClientQuery.value.toLowerCase();
        return Object.values(client).some(value =>
          String(value).toLowerCase().includes(searchTerm)
        );
      });
  });
</script>

<template>
  <div class="max-w-4xl mx-auto p-6">
    <!-- Títol -->
    <h1 class="text-3xl font-bold text-yellow-400 mb-4">
      Clients <span class="text-black">telecom</span>
    </h1>

    <!-- Indicador de càrrega -->
    <div v-if="loading" class="text-center text-gray-500">
      <p>Carregant clients...</p>
    </div>

    <div v-else-if="clients.length">
      <!-- Input de filtrat -->
      <input 
        v-model="searchClientQuery" 
        type="text" 
        placeholder="Filtrar per clients..."
        class="border border-gray-300 p-2 rounded w-full mb-2"
      />
      
      <!-- Si no hay productos, mostrar mensaje -->
      <p v-if="filteredClients.length === 0" class="text-center text-gray-500 py-4">
        No hi ha clients disponibles.
      </p>

      <!-- Targeta client -->
      <div v-else v-for="client in filteredClients" :key="client._id" class="bg-white shadow-md rounded-lg p-6 mb-6 border border-gray-200">
        <div class="flex justify-between items-center">
          <div>
            <h2 class="text-xl font-semibold text-gray-800">{{ client.givenName }} {{ client.familyName1 }}</h2>
            <p class="text-gray-500">{{ client.email }}</p>
            <p class="text-gray-500">{{ client.phone }}</p>
            <p class="text-gray-400 text-sm">{{ client.docType.toUpperCase() }}: {{ client.docNum }}</p>
          </div>
          <!-- Botó per mostrar/ocultar els productes -->
          <button 
            @click="toggleProductes(client.customerId)"
            class="bg-yellow-400 px-4 py-2 rounded-lg hover:bg-yellow-300 cursor-pointer"
          >
            {{ activeClient === client.customerId ? 'Ocultar Productes' : 'Veure Productes' }}
          </button>
        </div>

        <!-- Llista de productes -->
        <Transition name="slide-fade">
          <ProducteList v-if="activeClient === client.customerId" :productes="client.productes" />
        </Transition>
      </div>
    </div>

    <!-- Si no hi ha clients -->
    <div v-else class="text-gray-500 text-center mt-10">
      <p>No hi ha clients disponibles</p>
    </div>
  </div>
</template>

<style scoped>
  /* Animació per la transició */
  .slide-fade-enter-active,
  .slide-fade-leave-active {
    transition: all 0.5s ease;
  }
  .slide-fade-enter-from,
  .slide-fade-leave-to {
    transform: translateY(-20px);
    opacity: 0;
  }
</style>