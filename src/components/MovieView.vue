<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"

const search = ref('')
const movies = ref([])
const myMovies = ref([])
const showDropdown = ref(false)


// 🔍 Suche
const searchMovies = async () => {
  if (!search.value.trim()) {
    movies.value = []
    return
  }

  const res = await fetch(
    `https://api.themoviedb.org/3/search/multi?api_key=a57b9c639bd4d8f0ec3b12594c9fbdfb&query=${search.value}`
  )

  const data = await res.json()
  movies.value = data.results.slice(0, 10)
  showDropdown.value = true
}


// 📥 laden
const loadMovies = async () => {
  const res = await fetch(`${API}/api/movies`)
  myMovies.value = await res.json()
}


// ➕ hinzufügen
const addMovie = async (movie) => {
  await fetch(`${API}/api/movies`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      id: movie.id,
      name: movie.title || movie.name,
      image: movie.poster_path
        ? `https://image.tmdb.org/t/p/w500${movie.poster_path}`
        : '',
      status: 'watchlist'
    })
  })

  resetSearch()
  loadMovies()
}


// ❌ löschen
const deleteMovie = async (id) => {
  await fetch(`${API}/api/movies/${id}`, {
    method: 'DELETE'
  })

  myMovies.value = myMovies.value.filter(m => m.id !== id)
}


// 🔁 STATUS UPDATE (Backend)
const updateStatus = async (movie, status) => {
  await fetch(`${API}/api/movies/${movie.id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ status })
  })

  movie.status = status
}


// 🔍 reset search
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}


onMounted(loadMovies)
</script>

<template>
  <h2>🎬 Movies & Serien</h2>

  <!-- SEARCH -->
  <div class="search-wrapper">
    <input
      v-model="search"
      placeholder="Film oder Serie suchen..."
      @input="searchMovies"
      @focus="showDropdown = true"
    />

    <div v-if="showDropdown && movies.length" class="dropdown">
      <div
        v-for="movie in movies"
        :key="movie.id"
        class="dropdown-item"
        @click="addMovie(movie)"
      >
        <img
          v-if="movie.poster_path"
          :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path"
        />
        <span>{{ movie.title || movie.name }}</span>
      </div>
    </div>
  </div>

  <!-- LIST -->
  <h2>📋 Meine Liste</h2>

  <div class="games">
    <div v-for="m in myMovies" :key="m.id" class="card">

      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <p class="status">
        Status: {{ m.status || 'watchlist' }}
      </p>

      <div class="buttons">
        <button class="watched" @click="updateStatus(m, 'watched')">
          ✅ Gesehen
        </button>

        <button class="rewatch" @click="updateStatus(m, 'rewatch')">
          🔄 Rewatch
        </button>

        <button class="back" @click="updateStatus(m, 'watchlist')">
          ↩️ Liste
        </button>

        <button class="delete" @click="deleteMovie(m.id)">
          ❌
        </button>
      </div>

    </div>
  </div>
</template>

<style>
body {
  font-family: Arial, sans-serif;
  background:
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),
    url('/maxresdefault.jpg') center/cover no-repeat fixed;

  color: #f1f5f9;
  margin: 0;
  padding: 20px;
}

h2 {
  text-align: center;
  margin-bottom: 15px;
  font-size: 22px;
}

/* SEARCH */
.search-wrapper {
  position: relative;
  width: 500px;
  margin: 0 auto 30px;
}

.search-wrapper input {
  width: 100%;
  padding: 14px;
  border-radius: 10px;
  border: none;
  background: #1e293b;
  color: white;
}

/* DROPDOWN */
.dropdown {
  position: absolute;
  top: 110%;
  width: 100%;
  background: #1e293b;
  border-radius: 10px;
  max-height: 260px;
  overflow-y: auto;
  z-index: 10;
}

.dropdown-item {
  display: flex;
  gap: 10px;
  padding: 10px;
  cursor: pointer;
}

.dropdown-item:hover {
  background: #334155;
}

.dropdown-item img {
  width: 45px;
  height: 45px;
  border-radius: 6px;
  object-fit: cover;
}

/* GRID */
.games {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 20px;
}

/* CARD */
.card {
  background: #1e293b;
  padding: 12px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.card img {
  width: 100%;
  height: 180px;
  border-radius: 8px;
  object-fit: cover;
}

/* STATUS */
.status {
  font-size: 12px;
  opacity: 0.7;
}

/* BUTTONS */
.buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

button {
  flex: 1;
  padding: 8px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  color: white;
  font-size: 13px;
}

.watched { background: #22c55e; }
.rewatch { background: #3b82f6; }
.back { background: #f59e0b; }
.delete { background: #ef4444; }
</style>
