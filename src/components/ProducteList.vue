<script setup>
  import { defineProps } from 'vue';

  const props = defineProps({
    productes: Array, // Llista de productes
  });
</script>

<template>
  <div class="mt-4">
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
        <tr v-for="producte in props.productes" :key="producte._id" class="hover:bg-gray-50">
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
