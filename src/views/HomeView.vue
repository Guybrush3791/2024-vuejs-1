<template>
  <div v-if="!newFarmerShow && !updateFarmerShow">
    <h1>Farmer:</h1>
    <button @click="addTestData">ADD TEST FARMERS & FARMS</button>
    <button @click="toggleNewFarmerShow">ADD NEW FARMER</button>
    <ul>
      <li v-for="farmer in farmers" :key="farmer.id">
        {{ farmer.name }}
        {{ farmer.surname }}:
        {{ farmer.age }}
        <br />
        {{ farmer.farm.name }} ({{ farmer.farm.city }})
        <br />
        <button @click="editFarmer(farmer.id)">EDIT</button>
        <button @click="deleteFarmer(farmer.id)">DELETE</button>
      </li>
    </ul>
  </div>
  <div v-if="newFarmerShow">
    <h1>New Farmer</h1>
    <label>Name</label>
    <br />
    <input type="text" v-model="newFarmerData.name" />
    <br />
    <label>Surname</label>
    <br />
    <input type="text" v-model="newFarmerData.surname" />
    <br />
    <label>Age</label>
    <br />
    <input type="number" v-model="newFarmerData.age" />
    <br />
    <label>Farm</label>
    <br />
    <select v-model="newFarmerData.farmId">
      <option v-for="farm in farms" :key="farm.id" :value="farm.id">
        {{ farm.name }} ({{ farm.city }})
      </option>
    </select>
    <br />
    <button @click="toggleNewFarmerShow">CANCEL</button>
    <button @click="saveNewFarmer">SAVE</button>
  </div>
  <div v-if="updateFarmerShow">
    <h1>Update Farmer</h1>
    <label>Name</label>
    <br />
    <input type="text" v-model="updateFarmerData.name" />
    <br />
    <label>Surname</label>
    <br />
    <input type="text" v-model="updateFarmerData.surname" />
    <br />
    <label>Age</label>
    <br />
    <input type="number" v-model="updateFarmerData.age" />
    <br />
    <label>Farm</label>
    <br />
    <select v-model="updateFarmerData.farmId">
      <option v-for="farm in farms" :key="farm.id" :value="farm.id">
        {{ farm.name }} ({{ farm.city }})
      </option>
    </select>
    <br />
    <button @click="toggleUpdateFarmerShow">CANCEL</button>
    <button @click="updateFarmer">SAVE</button>
  </div>
</template>

<script setup>
// DIPENDENZE
import { onMounted, ref } from 'vue'
import axios from 'axios'

// VARIABILI
const newFarmerShow = ref(false)
const updateFarmerShow = ref(false)

const farms = ref([])
const farmers = ref([])

const newFarmerData = ref({
  name: '',
  surname: '',
  age: 0,
  farmId: 0
})
const updateFarmerData = ref({})

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

  axios.get('http://localhost:8080/api/v1/farm').then((res) => {
    farms.value = res.data
  })
}

// FUNCTIONS
const toggleNewFarmerShow = () => {
  newFarmerShow.value = !newFarmerShow.value
}
const toggleUpdateFarmerShow = () => {
  updateFarmerShow.value = !updateFarmerShow.value
}

const saveNewFarmer = () => {
  console.log(JSON.stringify(newFarmerData.value, null, 2))

  axios
    .post('http://localhost:8080/api/v1/farmer', newFarmerData.value)
    .then((res) => {
      const savedFarmer = res.data

      farmers.value.push(savedFarmer)

      newFarmerData.value = {
        name: '',
        surname: '',
        age: 0,
        farmId: 0
      }

      toggleNewFarmerShow()
    })
    .catch((err) => {
      console.log('Error: ' + err)
    })
}

const editFarmer = (id) => {
  for (let x = 0; x < farmers.value.length; x++) {
    const farmer = farmers.value[x]

    if (farmer.id === id) {
      updateFarmerData.value = farmer
      console.log('udateFarmerData: ' + JSON.stringify(updateFarmerData.value, null, 2))

      updateFarmerData.value.farmId = farmer.farm.id
      break
    }
  }

  console.log('udateFarmerData: ' + JSON.stringify(updateFarmerData.value, null, 2))

  updateFarmerShow.value = true
}
const updateFarmer = () => {
  const axiosData = {
    name: updateFarmerData.value.name,
    surname: updateFarmerData.value.surname,
    age: updateFarmerData.value.age,
    farmId: updateFarmerData.value.farmId
  }

  axios
    .patch('http://localhost:8080/api/v1/farmer/' + updateFarmerData.value.id, axiosData)
    .then(() => {
      updateData()
      updateFarmerShow.value = false
    })
    .catch((err) => {
      console.log('Error: ' + err)
    })
}

const deleteFarmer = (id) => {
  axios
    .delete('http://localhost:8080/api/v1/farmer/' + id)
    .then(() => {
      farmers.value = farmers.value.filter((farmer) => farmer.id !== id)
    })
    .catch((err) => {
      console.log('Error: ' + err)
    })
}

onMounted(updateData)
</script>
