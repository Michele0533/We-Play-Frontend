<script setup>
import { ref, onMounted, computed } from "vue";

const movies = ref([]);

/* LOAD */
async function loadMovies() {
  const res = await fetch("http://localhost:3000/api/movies");
  movies.value = await res.json();
}
loadMovies();

/* 🔁 MOVE TO NEXT LIST */
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

/* 🧠 3 LISTS */
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
}

.column {
  flex: 1;
  background: #f7f7f7;
  padding: 10px;
  border-radius: 12px;
  min-height: 300px;
}

.card {
  background: white;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 10px;
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
</style>
