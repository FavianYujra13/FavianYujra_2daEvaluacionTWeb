<template>
  <div>
    <h2>Lista de Personajes</h2>

    <!-- Reemplazar el mensaje de carga por el nuevo componente -->
    <LoadingIndicator v-if="loading" />

    <!-- Reemplazar el mensaje de error por el nuevo componente -->
    <ErrorMessage v-else-if="error" :message="error" />

    <div v-else>
      <div class="grid">
        <PersonCard
          v-for="person in people"
          :key="person.uid"
          :person="person"
        />
      </div>

      <div class="pagination" v-if="totalPages > 1">
        <button :disabled="currentPage === 1" @click="currentPage--">Anterior</button>
        <span>Página {{ currentPage }} de {{ totalPages }}</span>
        <button :disabled="currentPage === totalPages" @click="currentPage++">Siguiente</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import axios from 'axios'
import PersonCard from './PersonCard.vue'
import LoadingIndicator from './LoadingIndicator.vue'  
import ErrorMessage from './ErrorMessage.vue' 

const people = ref([])
const loading = ref(true)
const error = ref(null)

const currentPage = ref(1)
const totalPages = ref(1)

const fetchPeople = async () => {
  loading.value = true
  try {
    const res = await axios.get(`https://www.swapi.tech/api/people?page=${currentPage.value}`)
    totalPages.value = res.data.total_pages

    const detailedPeople = await Promise.all(
      res.data.results.map(async (item) => {
        const detail = await axios.get(item.url)
        return detail.data.result.properties
      })
    )

    people.value = detailedPeople
    error.value = null
  } catch (err) {
    error.value = 'Error al cargar personajes.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

onMounted(fetchPeople)
watch(currentPage, fetchPeople)
</script>

<style scoped>
.grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin-top: 1.5rem;
}

.pagination button {
  padding: 0.5rem 1rem;
  font-weight: bold;
  border-radius: 5px;
  border: none;
  cursor: pointer;
}

.pagination button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
