<script setup>
import { ref, onMounted, computed } from "vue";

/* =========================
   STATE
========================= */
const movies = ref([]);

/* =========================
   LOAD MOVIES
========================= */
async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}

onMounted(loadMovies);

/* =========================
   MOVE STATUS (CLICK)
========================= */
function moveMovie(movie) {
  const order = ["watchlist", "seen", "rewatch"];

  const nextStatus =
    order[(order.indexOf(movie.status) + 1) % order.length];

  movie.status = nextStatus;

  fetch(`http://localhost:3000/api/movies/${movie.id}/status`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ status: nextStatus })
  });
}

/* =========================
   FILTER INTO COLUMNS
========================= */
const watchlist = computed(() =>
  movies.value.filter(m => m.status === "watchlist")
);

const seen = computed(() =>
  movies.value.filter(m => m.status === "seen")
);

const rewatch = computed(() =>
  movies.value.filter(m => m.status === "rewatch")
);
</script>

<template>
  <div class="board">

    <!-- 📌 WATCHLIST -->
    <div class="column">
      <h2>📌 Watchlist</h2>

      <div
        v-for="movie in watchlist"
        :key="movie.id"
        class="card"
        @click="moveMovie(movie)"
      >
        <img :src="movie.image" />
        <p>{{ movie.name }}</p>
      </div>
    </div>

    <!-- ✅ SEEN -->
    <div class="column">
      <h2>✅ Seen</h2>

      <div
        v-for="movie in seen"
        :key="movie.id"
        class="card"
        @click="moveMovie(movie)"
      >
        <img :src="movie.image" />
        <p>{{ movie.name }}</p>
      </div>
    </div>

    <!-- 🔁 REWATCH -->
    <div class="column">
      <h2>🔁 Rewatch</h2>

      <div
        v-for="movie in rewatch"
        :key="movie.id"
        class="card"
        @click="moveMovie(movie)"
      >
        <img :src="movie.image" />
        <p>{{ movie.name }}</p>
      </div>
    </div>

  </div>
</template>

<style scoped>
.board {
  display: flex;
  gap: 20px;
  align-items: flex-start;
  padding: 20px;
}

/* COLUMN */
.column {
  flex: 1;
  background: #f5f5f5;
  border-radius: 14px;
  padding: 12px;
  min-height: 400px;
}

.column h2 {
  text-align: center;
  font-size: 16px;
  margin-bottom: 10px;
}

/* CARD */
.card {
  background: white;
  margin-bottom: 10px;
  border-radius: 12px;
  padding: 10px;
  cursor: pointer;
  transition: 0.2s;
}

.card:hover {
  transform: scale(1.03);
}

img {
  width: 100%;
  border-radius: 8px;
}

p {
  margin-top: 6px;
  text-align: center;
  font-weight: bold;
}
</style>
