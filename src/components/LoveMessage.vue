<script setup>
import { ref, onMounted, computed } from "vue";

const movies = ref([]);
const filter = ref("watchlist");

async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}
loadMovies();

/* FILTER */
const filteredMovies = computed(() =>
  movies.value.filter(m => m.status === filter.value)
);

/* 🔁 CYCLE STATUS */
function cycleStatus(movie) {
  const order = ["watchlist", "seen", "rewatch"];

  const currentIndex = order.indexOf(movie.status);
  const nextStatus = order[(currentIndex + 1) % order.length];

  movie.status = nextStatus;

  fetch(`http://localhost:3000/api/movies/${movie.id}/status`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ status: nextStatus })
  });
}
</script>

<template>
  <!-- MINI FILTER (optional, nur oben) -->
  <div class="tabs">
    <span @click="filter = 'watchlist'">📌 Watchlist</span>
    <span @click="filter = 'seen'">✅ Seen</span>
    <span @click="filter = 'rewatch'">🔁 Rewatch</span>
  </div>

  <!-- LIST -->
  <div class="grid">
    <div v-for="movie in filteredMovies" :key="movie.id" class="card">
      
      <img :src="movie.image" />
      <h3>{{ movie.name }}</h3>

      <!-- 🔥 EINZIGER TOGGLE -->
      <div class="status" @click="cycleStatus(movie)">
        {{
          movie.status === "watchlist"
            ? "📌 Watchlist"
            : movie.status === "seen"
            ? "✅ Seen"
            : "🔁 Rewatch"
        }}
      </div>

    </div>
  </div>
</template>

<style scoped>
.tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
  font-weight: bold;
}

.tabs span {
  cursor: pointer;
  opacity: 0.6;
}

.tabs span:hover {
  opacity: 1;
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

.status {
  margin-top: 10px;
  padding: 6px;
  border-radius: 8px;
  background: #f2f2f2;
  cursor: pointer;
  text-align: center;
  font-weight: bold;
}

.status:hover {
  background: #ddd;
}
</style>
