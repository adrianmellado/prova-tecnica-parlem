<script setup>
  import { ref, onMounted } from 'vue';

  const clients = ref([]); // Llistat de clients
  const productes = ref([]); // Llistat de productes
  const activeClient = ref(null); // Client amb productes visibles
  const loading = ref(true); // Estat de càrrega

  onMounted(async () => {
    try {
      const [clientsData, productsData] = await Promise.all([
        fetch('http://localhost:3000/client').then(res => res.json()),
        fetch('http://localhost:3000/producte').then(res => res.json())
      ]);

      // Asignar dades
      clients.value = clientsData;
      productes.value = productsData;

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

  // Obtenir els productes contractats per un client segons el seu customerId
  const getProductesByClient = (customerId) => {
    return productes.value.filter(product => product.customerId === customerId);
  };
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
      <!-- Targeta client -->
      <div v-for="client in clients" :key="client._id" class="bg-white shadow-md rounded-lg p-6 mb-6 border border-gray-200">
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
          <div v-if="activeClient === client.customerId" class="mt-4">
            <h3 class="font-semibold text-gray-700 mb-2">Productes contractats:</h3>
            <table class="w-full border-collapse border border-yellow-500">
              <thead class="bg-yellow-400 opacity-75">
                <tr>
                  <th class="border border-gray-300 px-4 py-2 text-left">Nom</th>
                  <th class="border border-gray-300 px-4 py-2 text-center">Tipus</th>
                  <th class="border border-gray-300 px-4 py-2 text-right">Velocitat / Dades</th>
                  <th class="border border-gray-300 px-4 py-2 text-right">Número Terminal</th>
                  <th class="border border-gray-300 px-4 py-2 text-right">Data Compra</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="producte in getProductesByClient(client.customerId)" :key="producte._id" class="hover:bg-gray-50">
                  <td class="border border-gray-300 px-4 py-2">{{ producte.productName }}</td>
                  <td class="border border-gray-300 px-4 py-2 text-center">{{ producte.productTypeName.toUpperCase() }}</td>
                  <td class="border border-gray-300 px-4 py-2 text-right">
                    {{ producte.mbSpeed ? producte.mbSpeed + 'MB' : producte.gbData + 'GB' }}
                  </td>
                  <td class="border border-gray-300 px-4 py-2 text-right">{{ producte.numeracioTerminal }}</td>
                  <td class="border border-gray-300 px-4 py-2 text-right">{{ new Date(producte.soldAt).toLocaleDateString() }}</td>
                </tr>
              </tbody>
            </table>
          </div>
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