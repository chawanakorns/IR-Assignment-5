<script setup lang="ts">
import { ref, watch } from 'vue'
import { useRouter, useRoute } from 'vue-router'

const query = ref('')
const router = useRouter()
const route = useRoute()

query.value = (route.query.q as string) || ''

watch(query, (newQuery) => {
  router.replace({ path: '/', query: { q: newQuery || undefined } }) // Removes query if empty
})
</script>

<template>
  <div class="homepage">
    <div class="title">Split Search Engine</div>
    <div class="search-container">
      <input v-model="query" class="search-bar" placeholder="Enter search terms" />
    </div>
    <router-view />
  </div>
</template>

<style scoped>
.title {
  font-size: 48px;
  font-weight: bold;
  margin: 30px;
}

.homepage {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.search-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

.search-bar {
  width: 580px;
  padding: 12px;
  font-size: 16px;
  border: 1px solid #dfe1e5;
  border-radius: 24px;
  outline: none;
  box-shadow: none;
  transition: box-shadow 0.2s;
}

.search-bar:focus {
  box-shadow: 0 1px 6px rgba(32, 33, 36, 0.28);
  border-color: rgba(223, 225, 229, 0);
}
</style>
