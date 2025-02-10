<script setup>
import { ref, onMounted } from 'vue'


const props = defineProps(['codeData', 'selected']);



// onMounted(async () => {
//   const response = await fetch('http://localhost:3000/api/country/' + codeSelected.value);
//     const codeData = await response.json();
//     codeSelectedData.value = codeData;
// });
// </script>

<template>
  <div class="country-data">
    <table v-if="selected.length">
      <tr>
        <th>Nombre: </th>
        <td>{{ codeData['name'] }}</td>
        <th v-if="codeData['population']">Poblacion: </th>
        <td>{{ codeData['population'] }}</td>
      </tr>
      <tr>
        <th v-if="codeData['pib']"> PIB:</th>
        <td>{{ codeData['pib'] }} </td>
        <th>Área: </th>
        <td>{{ codeData['area'] }}km2</td>
      </tr>
      <tr>
        <th v-if="codeData['income']">Renta: </th>
        <td>{{ codeData['income'] }}</td>
        <th>PIB/Habitante</th>
        <td :class="{ red: (codeData['pib'] * codeData['population']) > codeData['income'] }">{{ codeData['pib'] * codeData['population']}}</td>
      </tr>
      <tr><th><button>Eliminar</button></th></tr>
    </table>
    
  </div>
</template>

<style scoped>
.red {
  color: red;
}
</style>