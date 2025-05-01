<template>
    <div>
      <h2>Lista de Planetas</h2>
      <div v-if="loading">Cargando planetas...</div>
      <div v-else-if="error">{{ error }}</div>
  
      <div v-else>
        <div class="grid">
          <PlanetCard
            v-for="planet in planets"
            :key="planet.uid"
            :planet="planet"
          />
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import axios from 'axios'
  import PlanetCard from './PlanetCard.vue'
  
  const planets = ref([])
  const loading = ref(true)
  const error = ref(null)
  
  const fetchPlanets = async () => {
    loading.value = true
    try {
      const res = await axios.get(`https://www.swapi.tech/api/planets`)
  
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
  
      planets.value = detailed
    } catch (err) {
      error.value = 'Error al cargar planetas.'
      console.error(err)
    } finally {
      loading.value = false
    }
  }
  
  onMounted(fetchPlanets)
  </script>
  
  <style scoped>
  .grid {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }
  </style>
  