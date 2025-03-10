<script setup>
  import { defineProps, ref, computed } from 'vue';

  const props = defineProps({
    productes: Array, // Llistat de productes
  });

  const searchProducteQuery = ref(""); // Recerca per nom
  const sortBy = ref("productName"); // Camp d'ordenació per defecte
  const sortDirection = ref("asc"); // Direcció ascendent

  // Función para alternar entre ascendente y descendente
  const toggleSort = (field) => {
    if (sortBy.value === field) {
      sortDirection.value = sortDirection.value === "asc" ? "desc" : "asc";
    } else {
      sortBy.value = field;
      sortDirection.value = "asc";
    }
  };

  // Computed per filtrar i ordenar productes
  const filteredAndSortedProductes = computed(() => {
    return [...props.productes]
      .filter(producte => 
        producte.productName.toLowerCase().includes(searchProducteQuery.value.toLowerCase())
      )
      .sort((a, b) => {
        let modifier = sortDirection.value === "asc" ? 1 : -1;
        if (a[sortBy.value] < b[sortBy.value]) return -1 * modifier;
        if (a[sortBy.value] > b[sortBy.value]) return 1 * modifier;
        return 0;
      });
  });
</script>

<template>
  <div class="mt-4">
    <!-- Input de filtrat -->
    <input 
      v-model="searchProducteQuery" 
      type="text" 
      placeholder="Filtrar per nom del producte..."
      class="border border-gray-300 p-2 rounded w-full mb-2"
    />

    <h3 class="font-semibold text-gray-700 mb-2">Productes contractats:</h3>
    
    <!-- Si no hay productos, mostrar mensaje -->
    <p v-if="filteredAndSortedProductes.length === 0" class="text-center text-gray-500 py-4">
      No hi ha productes disponibles.
    </p>

    <!-- Taula de productes -->
    <table v-else class="w-full border-collapse border border-yellow-500">
      <thead class="bg-yellow-400 opacity-75">
        <tr>
          <th @click="toggleSort('productName')" class="border border-gray-300 px-4 py-2 text-left cursor-pointer">
            Nom {{ sortBy === 'productName' ? 'ᛨ' : '' }}
          </th>
          <th @click="toggleSort('productTypeName')" class="border border-gray-300 px-4 py-2 text-center cursor-pointer">
            Tipus {{ sortBy === 'productTypeName' ? 'ᛨ' : '' }}
          </th>
          <th @click="toggleSort('mbSpeed')" class="border border-gray-300 px-4 py-2 text-right cursor-pointer">
            Velocitat / Dades {{ sortBy === 'mbSpeed' ? 'ᛨ' : '' }}
          </th>
          <th @click="toggleSort('numeracioTerminal')" class="border border-gray-300 px-4 py-2 text-right cursor-pointer">
            Número Terminal {{ sortBy === 'numeracioTerminal' ? 'ᛨ' : '' }}
          </th>
          <th @click="toggleSort('soldAt')" class="border border-gray-300 px-4 py-2 text-right cursor-pointer">
            Data Compra {{ sortBy === 'soldAt' ? 'ᛨ' : '' }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="producte in filteredAndSortedProductes" :key="producte._id" class="hover:bg-gray-50">
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
</template>
