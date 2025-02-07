<script lang="ts">
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

export default {
  setup() {
    const route = useRoute()
    const query = route.query.q || ''
    const results = ref({
      bm25: [],
      tfidf: [],
    })
    const executionTime = ref(0)
    const loading = ref(true)
    const totalBm25Results = ref(0)
    const totalTfidfResults = ref(0)

    onMounted(async () => {
      try {
        const response = await axios.get(`http://localhost:5000/search?q=${query}`)
        results.value.bm25 = response.data.results_bm25
        results.value.tfidf = response.data.results_tfidf
        executionTime.value = response.data.execution_time
        totalBm25Results.value = response.data.total_bm25_results
        totalTfidfResults.value = response.data.total_tfidf_results
      } catch (error) {
        console.error('Error fetching results:', error)
      }
      loading.value = false
    })

    return { query, results, executionTime, loading, totalBm25Results, totalTfidfResults }
  },
}
</script>

<template>
  <div class="results-page">
    <h2>Results for "{{ query }}"</h2>
    <div class="results-container">
      <div class="results-section">
        <h3>BM25</h3>
        <p v-if="loading">Loading...</p>
        <p v-else>
          Time: {{ executionTime }}s | Results: {{ totalBm25Results }}
        </p>
        <ul>
          <li v-for="result in results.bm25" :key="result.url">
            <a :href="result.url" target="_blank">{{ result.title }}</a>
            <p>{{ result.text }}...</p>
            <span>Score: {{ result.score }}</span>
          </li>
        </ul>
      </div>

      <div class="results-section">
        <h3>TF-IDF</h3>
        <p v-if="loading">Loading...</p>
        <p v-else>
          Time: {{ executionTime }}s | Results: {{ totalTfidfResults }}
        </p>
        <ul>
          <li v-for="result in results.tfidf" :key="result.url">
            <a :href="result.url" target="_blank">{{ result.title }}</a>
            <p>{{ result.text }}...</p>
            <span>Score: {{ result.score }}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

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
