<script lang="ts">
import axios from 'axios'
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

export default {
  setup() {
    const route = useRoute()
    const query = ref(route.query.q || '')
    const bm25Results = ref([])
    const tfidfResults = ref([])
    const executionTime = ref(0)
    const loading = ref(false)
    const totalResults = ref(0)

    const fetchResults = async () => {
      if (!query.value.trim()) return
      loading.value = true
      try {
        const response = await axios.get(`http://localhost:5000/search?q=${query.value}`)
        bm25Results.value = response.data.bm25_results
        tfidfResults.value = response.data.tfidf_results
        executionTime.value = response.data.execution_time
        totalResults.value = response.data.total_results
      } catch (error) {
        console.error('Error fetching results:', error)
      }
      loading.value = false
    }

    watch(() => route.query.q, (newQuery) => {
      query.value = newQuery || ''
      fetchResults()
    }, { immediate: true })

    return { query, bm25Results, tfidfResults, executionTime, loading, totalResults }
  }
}
</script>

<template>
  <div class="results-page" v-if="query">
    <h1>Results for "<span class="bold-query">{{ query }}</span>"</h1>
    <p v-if="loading">Loading...</p>
    <div v-else>
      <p class="result-count">About {{ totalResults }} results ({{ executionTime }} seconds)</p>

      <div class="results-container">
        <!-- BM25 Results -->
        <div class="results-box">
          <h2 style="text-decoration: underline; padding: 10px;">BM25 Results</h2>
          <div class="results-scrollable">
            <div class="result" v-for="result in bm25Results" :key="result.url">
              <a :href="result.url" target="_blank" class="result-title">{{ result.title }}</a>
              <div class="result-url">{{ result.url }}</div>
              <div class="result-snippet" v-html="result.highlighted_text"></div>
              <div class="result-score">BM25 Score: {{ result.score }}</div>
            </div>
          </div>
        </div>

        <!-- TF-IDF Results -->
        <div class="results-box">
          <h2 style="text-decoration: underline; padding: 10px;">TF-IDF Results</h2>
          <div class="results-scrollable">
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
  </div>
</template>

<style scoped>
.results-page {
  margin: 20px;
  font-family: Arial, sans-serif;
}

.bold-query {
  font-weight: bold;
  color: #d93025;
}

.result-count {
  font-size: 16px;
  color: #777;
}

.results-container {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
  margin-bottom: 50px;
  width: 100%;
}

.results-box {
  width: 48%;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  background-color: #fff;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  text-align: left;
}

.results-scrollable {
  max-height: 400px;
  overflow-y: auto;
  padding-right: 10px;
}

.result {
  border-bottom: 1px solid #ccc;
  margin-bottom: 20px;
  padding: 10px;
}

.result-title {
  font-size: 16px;
  font-weight: bold;
  color: #1a0dab;
  text-decoration: none;
}

.result-title:hover {
  text-decoration: underline;
}

.result-url {
  font-size: 12px;
  color: #006621;
  margin-top: 5px;
  word-break: break-word;
}

.result-snippet {
  margin-top: 12px;
  font-size: 14px;
  color: #4d5156;
  white-space: normal;
  /* Fix text wrapping */
  overflow: hidden;
}

.result-snippet b {
  font-weight: bold;
  color: #d93025;
}

.result-score {
  margin-top: 5px;
  font-size: 12px;
  color: #888;
}
</style>
