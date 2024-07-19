<template>
  <div v-if="!newFarmShow && !updateFarmShow">
    <h1>Farm:</h1>
    <button @click="addTestData">ADD TEST FARMERS & FARMS</button>
    <br />
    <button @click="toggleNewFarmShow">ADD NEW FARM</button>
    <ul>
      <li v-for="farm in farms" :key="farm.id">
        [{{ farm.id }}] {{ farm.name }}: {{ farm.city }}
        <br />
        <button @click="editFarm(farm.id)">EDIT</button>
        <button @click="deleteFarm(farm.id)">DELETE</button>
      </li>
    </ul>
  </div>
  <div v-if="newFarmShow">
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
  <div v-if="updateFarmShow">
    <h1>Update Farm:</h1>
    <label>Name</label>
    <br />
    <input type="text" v-model="updateFarmData.name" />
    <br />
    <label>City</label>
    <br />
    <input type="text" v-model="updateFarmData.city" />
    <br />
    <button @click="toggleUpdateFarmShow">CANCEL</button>
    <button @click="updateFarm">SAVE</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

const farms = ref([])

const newFarmShow = ref(false)
const updateFarmShow = ref(false)
const updateFarmData = ref({})

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
const toggleUpdateFarmShow = () => {
  updateFarmShow.value = !updateFarmShow.value
}

const saveNewFarm = () => {
  axios.post('http://localhost:8080/api/v1/farm', newFarmData.value).then((res) => {
    const savedFarm = res.data

    farms.value.push(savedFarm)
    newFarmData.value = {
      name: '',
      city: ''
    }

    toggleNewFarmShow()
  })
}

const editFarm = (id) => {
  for (let x = 0; x < farms.value.length; x++) {
    const farm = farms.value[x]

    if (farm.id === id) {
      updateFarmData.value = farm
      break
    }
  }

  updateFarmShow.value = true
}

const updateFarm = () => {
  const axiosData = {
    name: updateFarmData.value.name,
    city: updateFarmData.value.city
  }

  axios
    .patch('http://localhost:8080/api/v1/farm/' + updateFarmData.value.id, axiosData)
    .then(() => {
      updateData()
      toggleUpdateFarmShow()
    })
}

const deleteFarm = (id) => {
  axios.delete('http://localhost:8080/api/v1/farm/' + id).then(() => {
    farms.value = farms.value.filter((farm) => farm.id !== id)
  })
}

onMounted(() => {
  updateData()
})
</script>
