<template>
  <div v-if="!newFarmShow">
    <h1>Farm:</h1>
    <button @click="addTestData">ADD TEST FARMERS & FARMS</button>
    <br />
    <button @click="toggleNewFarmShow">ADD NEW FARM</button>
    <ul>
      <li v-for="(farm, ind) in farms" :key="farm.id">
        [{{ ind + 1 }}] {{ farm.name }}: {{ farm.city }}
      </li>
    </ul>
    <p v-if="saveResVis">
      {{ saveRes }}
    </p>
  </div>
  <div v-else>
    <h1>New Farm:</h1>
    <label>Name</label>
    <br />
    <input type="text" v-model="newFarmData.name" />
    <br />
    <label>City</label>
    <br />
    <input type="text" v-model="newFarmData.city" />
    <br />
    <button @click="toggleNewFarmShow">CANCEL</button>
    <button @click="saveNewFarm">SAVE</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const farms = ref([])

const saveResVis = ref(false)
const saveRes = ref('')
const newFarmShow = ref(false)
const newFarmData = ref({
  name: '',
  city: ''
})

const updateData = () => {
  axios
    .get('http://localhost:8080/api/v1/farm')
    .then((res) => {
      farms.value = res.data
    })
    .catch((err) => {
      console.log('Error: ' + err)
    })
}

const addTestData = () => {
  axios
    .get('http://localhost:8080/api/v1/farmer/test/add')
    .then(updateData)
    .catch((err) => console.log('Error: ' + err))
}

const toggleNewFarmShow = () => {
  newFarmShow.value = !newFarmShow.value

  console.log('newFarmShow: ' + newFarmShow.value)
}

const saveNewFarm = () => {
  axios.post('http://localhost:8080/api/v1/farm', newFarmData.value).then((res) => {
    const savedFarm = res.data

    farms.value.push(savedFarm)
    newFarmData.value = {
      name: '',
      city: ''
    }

    saveRes.value = 'update'
    saveResVis.value = true

    toggleNewFarmShow()
  })
}

onMounted(() => {
  updateData()
})
</script>

<!-- 

    TODO:
    
    Completare il componente facendo in modo che alla
    creazione del componente vengano scaricati i dati di 
    tutte le fattorie (farm).

    Aggiungere inoltre un tasto che permetta di caricare
    dei dati di test per contadini e fattorie (/test/add),
    facendo in modo che i dati visibili in pagina vengano
    aggiornati in maniera automatica e nascosta all'utente.

-->
