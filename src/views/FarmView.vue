<template>
  <h1>Farm:</h1>
</template>

<script setup>
import { onMounted, ref } from 'vue'

onMounted(() => {
  console.log('Hello from FarmView')
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
