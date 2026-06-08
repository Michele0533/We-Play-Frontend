<script setup>
import { ref, onMounted, computed } from "vue";

const movies = ref([]);

/* =========================
   LOAD
========================= */
async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}

onMounted(loadMovies);

/* =========================
   MOVE MOVIE (SAFE VERSION)
========================= */
function moveMovie(movie) {
  const order = ["watchlist", "seen", "rewatch"];

  // 🔥 safe fallback
  const current = order.includes(movie.status)
    ? movie.status
    : "watchlist";

  const next = order[(order.indexOf(current) + 1) % order.length];

  movie.status = next;

  fetch(`http://localhost:3000/api/movies/${movie.id}/status`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ status: next })
  }).catch(err => {
    console.log("Update failed", err);
  });
}

/* =========================
   SAFE LISTS (NO BUGS EVER)
========================= */
const watchlist = computed(() =>
  movies.value.filter(m => !m.status || m.status === "watchlist")
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
  padding: 20px;
}

.column {
  flex: 1;
  background: #f2f2f2;
  border-radius: 12px;
  padding: 10px;
  min-height: 400px;
}

.card {
  background: white;
  margin-bottom: 10px;
  border-radius: 10px;
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
  text-align: center;
  font-weight: bold;
}
</style>
