<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const query = ref('');
const router = useRouter();

const search = () => {
  if (query.value.trim()) {
    router.push({ path: '/search', query: { q: query.value } });
  }
};
</script>

<template>
  <div class="homepage">
    <div class="search-container">
      <input v-model="query" @keyup.enter="search" class="search-bar" placeholder="Search Google or type a URL" />
      <div class="buttons">
        <button @click="search" class="search-button">Google Search</button>
        <button class="search-button">I'm Feeling Lucky</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.homepage {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  text-align: center;
}

.google-logo {
  width: 272px;
  margin-bottom: 20px;
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

.buttons {
  margin-top: 20px;
}

.search-button {
  background-color: #f8f9fa;
  border: 1px solid #f8f9fa;
  padding: 10px 20px;
  font-size: 14px;
  margin: 5px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.3s;
}

.search-button:hover {
  background-color: #e8e8e8;
}
</style>
