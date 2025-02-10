<script setup>
import { computed, onMounted, ref } from 'vue'
import codes from "./data/codes.json"; 
import CountryData from './components/CountryData.vue';
import GoogleChart from './components/GoogleChart.vue';

const name = ref('');
const selected = ref([]);
const codeSelected = ref('');
const codesNames = ref([]);
const codeData = ref([]);

const dataTypes = ['population', 'pib', 'area', 'income'];

async function selectedCode(code) {
  if (!selected.value.includes(code)) {
    const response = await fetch('http://localhost:3000/api/country/' + code);
    const data = await response.json();
    codeData.value = data;
    codeSelected.value = code;
    if (codeData) {
      selected.value.push(code);
        selected.value.sort((a, b) => {
        const nameA = codesNames.value[a].toUpperCase();
        const nameB = codesNames.value[b].toUpperCase();
        if (nameA < nameB) {
          return -1;
        }
        if (nameA > nameB) {
          return 1;
        }
        return 0;
      });
    }
  }
}

function removeSelectedCode(code) {
  selected.value = selected.value.filter((item) => item !== code);
}

onMounted(async () => {
  const response = await fetch('http://localhost:3000/api/names');
  const data = await response.json();
  codesNames.value = data;
  console.log(data);
});
</script>

<template>
  <div class="parent">
    <div class="header">
      <h1>Datos mundiales</h1>
    </div>

    <div class="name">
      <input type="text" v-model="name">
      <p v-if="name">Tu nombre es {{ name }} tiene {{ name.length }} letras</p>
    </div>

    <div class="div-codes">
      <h2>Códigos: </h2>
      <ul>
        <li v-for="code in codes" :key="code" @click="selectedCode(code)">{{ codesNames[code] }}</li>
      </ul>
    </div>

    <div class="selected">
      <h2>Seleccionado {{ selected.length }}: </h2>
      <p v-if="!selected.length">No has seleccionado algún país en el panel de códigos</p>
      <ul>
        <li v-for="select in selected" :key="select" @input="orderCodes">
          {{ codesNames[select] }}
          <button @click="removeSelectedCode(select)">Desmarcar</button>
        </li>
      </ul>
    </div>

    <div class="country-data">
      <h2 v-if="!selected.length">No hay datos que mostrar</h2>
      <CountryData :codeData="codeData" :selected="selected"/>
    </div>

    <div class="options">
      <span v-for="dataType in dataTypes"  :key="dataType">
        <label>{{ dataType }}</label>
        <input type="radio" :value="dataType" name="dataType">
      </span>
    </div>

    <div class="chart">
      <GoogleChart :data="codeData"/>
    </div>

  </div>
</template>

<style scoped>
.parent {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: auto repeat(3, 1fr);
  grid-column-gap: 0px;
  grid-row-gap: 0px;
  height: 100vh;
  margin: -2rem;
}

.header {
  grid-area: 1 / 1 / 2 / 6;
  background-color: aquamarine;
}

.header h1 {
  margin: 2px;
}

.div-codes {
  grid-area: 2 / 1 / 6 / 2;
  background-color: azure;
  overflow-y: auto;
}

.name {
  grid-area: 2 / 2 / 3 / 3;
  background-color: antiquewhite;
}

.selected {
  grid-area: 2 / 5 / 4 / 6;
  background-color: lightyellow;
  overflow-y: auto;
}

.country-data {
  grid-area: 2 / 3 / 3 / 5;
  background-color: lightseagreen;
}

.options {
  grid-area: 3 / 2 / 4 / 5;
  background-color: bisque;
}

.chart {
  grid-area: 4 / 2 / 5 / 6;
  background-color: aqua;
}

ul li {
  text-align: left;
  font-size: 0.9rem;
}

ul li:hover {
  font-weight: bold;
  cursor: pointer
}

ul li button {
  font-size: 0.6rem;
  margin: 2px;
}

h2 {
  font-size: 1.2rem;
}
</style>