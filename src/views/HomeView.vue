<template>
  <h1>Hello World from Home</h1>
  <ul>
    <li v-for="farmer in farmers" :key="farmer.id">
      {{ farmer.name }}
      {{ farmer.surname }}:
      {{ farmer.age }}
    </li>
  </ul>
</template>

<script setup>
// DIPENDENZE
import { onMounted, ref } from 'vue'
import axios from 'axios'

// VARIABILI
const farmers = ref([])

// EVENTI
onMounted(() => {
  axios
    .get('http://localhost:8080/api/v1/farmer')
    .then((res) => {
      console.log(res.data)

      farmers.value = res.data
    })
    .catch((err) => {
      console.log('error!!!!!!!!!')
    })
})
</script>
