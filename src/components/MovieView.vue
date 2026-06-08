<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"

const search = ref('')
const movies = ref([])
const myMovies = ref([])
const watchedMovies = ref([])
const rewatchMovies = ref([])
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

// 📥 Liste laden
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
        : ''
    })
  })

  showDropdown.value = false
  search.value = ''
  movies.value = []

  loadMovies()
}

// ❌ löschen
const deleteMovie = async (id) => {
  await fetch(`${API}/api/movies/${id}`, {
    method: 'DELETE'
  })

  loadMovies()
}

// ✅ Gesehen
const moveToWatched = (movie) => {
  watchedMovies.value.push(movie)
  myMovies.value = myMovies.value.filter(m => m.id !== movie.id)
}

// 🔄 Rewatch
const moveToRewatch = (movie) => {
  rewatchMovies.value.push(movie)
  myMovies.value = myMovies.value.filter(m => m.id !== movie.id)
}

onMounted(loadMovies)
</script>

<template>
  <h2>🎬 Movies & Serien</h2>

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

  <h2>📋 Meine Liste</h2>

  <div class="games">
    <div v-for="m in myMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched-btn" @click="moveToWatched(m)">
          ✅ Gesehen
        </button>

        <button class="rewatch-btn" @click="moveToRewatch(m)">
          🔄 Rewatch
        </button>

        <button class="delete-btn" @click="deleteMovie(m.id)">
          ❌
        </button>
      </div>
    </div>
  </div>

  <h2>✅ Gesehen</h2>

  <div class="games">
    <div v-for="m in watchedMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>
    </div>
  </div>

  <h2>🔄 Rewatch</h2>

  <div class="games">
    <div v-for="m in rewatchMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>
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

.search-wrapper {
  position: relative;
  width: 500px;
  margin: 0 auto 30px auto;
}

.search-wrapper input {
  width: 100%;
  padding: 14px;
  font-size: 16px;
  border-radius: 10px;
  border: none;
  outline: none;
  background: #1e293b;
  color: white;
}

.dropdown {
  position: absolute;
  top: 110%;
  left: 0;
  right: 0;
  background: #1e293b;
  border-radius: 10px;
  max-height: 260px;
  overflow-y: auto;
  z-index: 10;
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 12px;
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

.games {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.card {
  background: #1e293b;
  border-radius: 12px;
  overflow: hidden;
  padding: 12px;
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

.card h3 {
  margin: 0;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.watched-btn {
  background: #22c55e;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 6px;
  cursor: pointer;
}

.watched-btn:hover {
  background: #16a34a;
}

.rewatch-btn {
  background: #3b82f6;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 6px;
  cursor: pointer;
}

.rewatch-btn:hover {
  background: #2563eb;
}

.delete-btn {
  background: #ef4444;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 6px;
  cursor: pointer;
}

.delete-btn:hover {
  background: #dc2626;
}
</style>
