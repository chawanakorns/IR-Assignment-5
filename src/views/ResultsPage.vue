<template>
  <div class="results-page">
    <h2>Results for "{{ query }}"</h2>
    <p v-if="loading">Loading...</p>
    <p v-else>Execution Time: {{ executionTime }}s</p>
    <div class="results-container">
      <div class="results-section">
        <h3>BM25 Results</h3>
        <ul>
          <li v-for="result in results.bm25" :key="result._id">{{ result._source.content }}</li>
        </ul>
      </div>
      <div class="results-section">
        <h3>TF-IDF Results</h3>
        <ul>
          <li v-for="result in results.tfidf" :key="result._id">{{ result._source.content }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

export default {
  setup() {
    const route = useRoute()
    const query = route.query.q || ''
    const results = ref({ bm25: [], tfidf: [] })
    const executionTime = ref(0)
    const loading = ref(true)

    onMounted(async () => {
      try {
        const response = await axios.get(`http://localhost:5000/search?q=${query}`)
        results.value = response.data
        executionTime.value = response.data.execution_time
      } catch (error) {
        console.error('Error fetching results:', error)
      }
      loading.value = false
    })

    return { query, results, executionTime, loading }
  },
}
</script>

<style>
.results-container {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.results-section {
  width: 45%;
}
</style>
