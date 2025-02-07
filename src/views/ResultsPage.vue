<script lang="ts">
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

export default {
  setup() {
    const route = useRoute()
    const query = route.query.q || ''
    const bm25Results = ref([])
    const tfidfResults = ref([])
    const executionTime = ref(0)
    const loading = ref(true)
    const totalResults = ref(0)

    onMounted(async () => {
      try {
        const response = await axios.get(`http://localhost:5000/search?q=${query}`)
        bm25Results.value = response.data.bm25_results
        tfidfResults.value = response.data.tfidf_results
        executionTime.value = response.data.execution_time
        totalResults.value = response.data.total_results
      } catch (error) {
        console.error('Error fetching results:', error)
      }
      loading.value = false
    })

    return { query, bm25Results, tfidfResults, executionTime, loading, totalResults }
  },
}
</script>

<template>
  <div class="results-page">
    <h1>Results for "{{ query }}"</h1>
    <p v-if="loading">Loading...</p>
    <div v-else>
      <p class="result-count">About {{ totalResults }} results ({{ executionTime }} seconds)</p>

      <div class="results-container">
        <!-- BM25 Results -->
        <div class="results-section">
          <h2 style="text-decoration: underline;">BM25 Results</h2>
          <br>
          <div class="result" v-for="result in bm25Results" :key="result.url">
            <a :href="result.url" target="_blank" class="result-title">{{ result.title }}</a>
            <div class="result-url">{{ result.url }}</div>
            <div class="result-snippet" v-html="result.highlighted_text"></div>
            <div class="result-score">BM25 Score: {{ result.score }}</div>
          </div>
        </div>

        <!-- TF-IDF Results -->
        <div class="results-section">
          <h2 style="text-decoration: underline;">TF-IDF Results</h2>
          <br>
          <div class="result" v-for="result in tfidfResults" :key="result.url">
            <a :href="result.url" target="_blank" class="result-title">{{ result.title }}</a>
            <div class="result-url">{{ result.url }}</div>
            <div class="result-snippet" v-html="result.highlighted_text"></div>
            <div class="result-score">TF-IDF Score: {{ result.score }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.results-page {
  margin: 20px;
  font-family: Arial, sans-serif;
}

.result-count {
  font-size: 16px;
  color: #777;
}

.results-container {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.results-section {
  width: 48%;
}

.result {
  border-bottom: 1px solid #ccc;
  padding-bottom: 10px;
  margin-bottom: 20px;
}

.result-title {
  font-size: 18px;
  font-weight: bold;
  color: #1a0dab;
  text-decoration: none;
}

.result-title:hover {
  text-decoration: underline;
}

.result-url {
  font-size: 14px;
  color: #006621;
  margin-top: 5px;
  inline-size: 100%;
  overflow: hidden;
}

.result-snippet {
  margin-top: 10px;
  font-size: 14px;
  color: #4d5156;
}

.result-snippet b {
  font-weight: bold;
}

.result-score {
  margin-top: 5px;
  font-size: 12px;
  color: #888;
}
</style>
