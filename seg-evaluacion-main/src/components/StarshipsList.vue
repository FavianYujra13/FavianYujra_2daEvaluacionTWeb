<template>
    <div>
      <h2>Lista de Naves Estelares</h2>
      <div v-if="loading">Cargando naves...</div>
      <div v-else-if="error">{{ error }}</div>
  
      <div v-else>
        <div class="grid">
          <StarshipCard
            v-for="ship in starships"
            :key="ship.uid"
            :ship="ship"
          />
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import axios from 'axios'
  import StarshipCard from './StarshipCard.vue'
  
  const starships = ref([])
  const loading = ref(true)
  const error = ref(null)
  
  const fetchStarships = async () => {
    loading.value = true
    try {
      const res = await axios.get(`https://www.swapi.tech/api/starships`)
  
      const detailed = await Promise.all(
        res.data.results.map(async (item) => {
          const detail = await axios.get(item.url)
          return {
            uid: item.uid,
            url: item.url,
            ...detail.data.result.properties
          }
        })
      )
  
      starships.value = detailed
    } catch (err) {
      error.value = 'Error al cargar naves estelares.'
      console.error(err)
    } finally {
      loading.value = false
    }
  }
  
  onMounted(fetchStarships)
  </script>
  
  <style scoped>
  .grid {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }
  </style>
  