<template>
  <h1>Hello World from Home</h1>
  <button @click="addTestData">ADD TEST DATA</button>
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
const addTestData = () => {
  axios.get('http://localhost:8080/api/v1/farmer/test/add').then(updateData)
}
const updateData = () => {
  axios
    .get('http://localhost:8080/api/v1/farmer')
    .then((res) => {
      farmers.value = res.data
    })
    .catch(() => {
      console.log('error!!!!!!!!!')
    })
}
// const updateData = async () => {
//   const res = await axios.get('http://localhost:8080/api/v1/farmer')

//   farmers.value = res.data

// }
// onMounted(() => {
//   updateData()
// })
onMounted(updateData)
</script>
