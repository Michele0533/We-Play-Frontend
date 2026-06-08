<script setup>
import { ref, onMounted, computed } from "vue";

/* =========================
   STATE
========================= */
const movies = ref([]);
const filter = ref("watchlist"); // watchlist | seen | rewatch

/* =========================
   LOAD DATA
========================= */
async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}

onMounted(loadMovies);

/* =========================
   FILTERED VIEW
========================= */
const filteredMovies = computed(() =>
  movies.value.filter(m => m.status === filter.value)
);

/* =========================
   UPDATE STATUS
========================= */
async function updateStatus(movie, newStatus) {
  movie.status = newStatus;

  await fetch(`http://localhost:3000/api/movies/${movie.id}/status`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ status: newStatus })
  });
}
</script>

<template>
  <!-- FILTER TABS -->
  <div class="tabs">
    <button @click="filter = 'watchlist'">📌 Watchlist</button>
    <button @click="filter = 'seen'">✅ Seen</button>
    <button @click="filter = 'rewatch'">🔁 Rewatch</button>
  </div>

  <!-- LIST -->
  <div class="grid">
    <div v-for="movie in filteredMovies" :key="movie.id" class="card">
      
      <img :src="movie.image" />
      <h3>{{ movie.name }}</h3>

      <!-- STATUS SWITCH -->
      <select
        v-model="movie.status"
        @change="updateStatus(movie, movie.status)"
      >
        <option value="watchlist">Watchlist</option>
        <option value="seen">Seen</option>
        <option value="rewatch">Rewatch</option>
      </select>

    </div>
  </div>
</template>

<style scoped>
.tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

button {
  padding: 8px 12px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  background: #eee;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
}

.card {
  background: #fff;
  padding: 10px;
  border-radius: 12px;
}
img {
  width: 100%;
  border-radius: 10px;
}
select {
  margin-top: 10px;
  width: 100%;
}
</style>
