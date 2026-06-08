<script setup>
import { ref, onMounted } from "vue";

const movies = ref([]);

/* =========================
   LOAD MOVIES
========================= */
async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}

onMounted(loadMovies);
</script>

<template>
  <div class="container">
    <h1>🎬 Movies</h1>

    <div class="grid">
      <div
        v-for="movie in movies"
        :key="movie.id"
        class="card"
      >
        <img :src="movie.image" />
        <p>{{ movie.name }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  margin-bottom: 20px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
}

.card {
  background: #fff;
  border-radius: 12px;
  padding: 10px;
  text-align: center;
  box-shadow: 0 4px 10px rgba(0,0,0,0.1);
}

img {
  width: 100%;
  border-radius: 10px;
}

p {
  margin-top: 8px;
  font-weight: bold;
}
</style>
