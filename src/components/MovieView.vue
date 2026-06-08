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


// 📥 Laden
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


// ❌ löschen (auch lokal entfernen)
const deleteMovie = async (id) => {
  await fetch(`${API}/api/movies/${id}`, { method: 'DELETE' })

  myMovies.value = myMovies.value.filter(m => m.id !== id)
  watchedMovies.value = watchedMovies.value.filter(m => m.id !== id)
  rewatchMovies.value = rewatchMovies.value.filter(m => m.id !== id)
}


// 📦 Status ändern
const setStatus = (movie, status) => {
  removeFromAll(movie)

  if (status === 'watchlist') myMovies.value.push(movie)
  if (status === 'watched') watchedMovies.value.push(movie)
  if (status === 'rewatch') rewatchMovies.value.push(movie)
}


// 🧹 aus allen Listen entfernen
const removeFromAll = (movie) => {
  myMovies.value = myMovies.value.filter(m => m.id !== movie.id)
  watchedMovies.value = watchedMovies.value.filter(m => m.id !== movie.id)
  rewatchMovies.value = rewatchMovies.value.filter(m => m.id !== movie.id)
}


// 🔁 Status-Wechsel Helfer
const toWatched = (movie) => setStatus(movie, 'watched')
const toRewatch = (movie) => setStatus(movie, 'rewatch')
const backToList = (movie) => setStatus(movie, 'watchlist')


// 🔍 Reset Search
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}


// 📥 initial laden + sortieren
onMounted(async () => {
  const res = await fetch(`${API}/api/movies`)
  const data = await res.json()

  data.forEach(m => {
    if (!m.status || m.status === 'watchlist') myMovies.value.push(m)
    else if (m.status === 'watched') watchedMovies.value.push(m)
    else if (m.status === 'rewatch') rewatchMovies.value.push(m)
  })
})
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


  <!-- WATCHLIST -->
  <h2>📋 Meine Liste</h2>

  <div class="games">
    <div v-for="m in myMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="toWatched(m)">✅ Gesehen</button>
        <button class="rewatch" @click="toRewatch(m)">🔄 Rewatch</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>


  <!-- WATCHED -->
  <h2>✅ Gesehen</h2>

  <div class="games">
    <div v-for="m in watchedMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="rewatch" @click="toRewatch(m)">🔄 Rewatch</button>
        <button class="back" @click="backToList(m)">↩️ Zur Liste</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>


  <!-- REWATCH -->
  <h2>🔄 Rewatch</h2>

  <div class="games">
    <div v-for="m in rewatchMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="toWatched(m)">✅ Gesehen</button>
        <button class="back" @click="backToList(m)">↩️ Zur Liste</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>
</template>


<style>
body {
  font-family: Arial;
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

.dropdown {
  position: absolute;
  top: 110%;
  width: 100%;
  background: #1e293b;
  border-radius: 10px;
  max-height: 260px;
  overflow-y: auto;
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
  object-fit: cover;
  border-radius: 6px;
}

.games {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 20px;
}

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
  object-fit: cover;
  border-radius: 8px;
}

.buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

button {
  flex: 1;
  padding: 6px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  color: white;
}

.watched { background: #22c55e; }
.rewatch { background: #3b82f6; }
.back { background: #f59e0b; }
.delete { background: #ef4444; }
</style>
