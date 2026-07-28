<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"

const search = ref('')
const movies = ref([])

const watchlist = ref([])
const watched = ref([])
const rewatch = ref([])

const showDropdown = ref(false)

/* =========================
 🔍 SEARCH
========================= */
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

/* =========================
 📥 LOAD + FIX STATUS BUG
========================= */
const loadMovies = async () => {
  const res = await fetch(`${API}/api/movies`)
  const data = await res.json()

  watchlist.value = []
  watched.value = []
  rewatch.value = []

  data.forEach(m => {
    const status = (m.status ?? 'watchlist').toLowerCase() // ✅ FIX

    if (status === 'watched') watched.value.push(m)
    else if (status === 'rewatch') rewatch.value.push(m)
    else watchlist.value.push(m)
  })
}

/* =========================
 ➕ ADD MOVIE
========================= */
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

/* =========================
 ⚡ LIVE MOVE STATUS
========================= */
const updateStatus = async (movie, status) => {
  movie.status = status

  watchlist.value = watchlist.value.filter(m => m.id !== movie.id)
  watched.value = watched.value.filter(m => m.id !== movie.id)
  rewatch.value = rewatch.value.filter(m => m.id !== movie.id)

  if (status === 'watched') watched.value.push(movie)
  else if (status === 'rewatch') rewatch.value.push(movie)
  else watchlist.value.push(movie)

  fetch(`${API}/api/movies/${movie.id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ status })
  })
}

/* =========================
 ❌ DELETE
========================= */
const deleteMovie = async (id) => {
  await fetch(`${API}/api/movies/${id}`, {
    method: 'DELETE'
  })

  loadMovies()
}

/* =========================
 🔍 RESET
========================= */
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}

/* =========================
 🚀 INIT
========================= */
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

  <h2>📋 Watchlist</h2>
  <div class="games">
    <div v-for="m in watchlist" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="updateStatus(m, 'watched')">✅</button>
        <button class="rewatch" @click="updateStatus(m, 'rewatch')">🔄</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>

  <h2>✅ Gesehen</h2>
  <div class="games">
    <div v-for="m in watched" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="rewatch" @click="updateStatus(m, 'rewatch')">🔄</button>
        <button class="back" @click="updateStatus(m, 'watchlist')">↩️</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>

  <h2>🔄 Rewatch</h2>
  <div class="games">
    <div v-for="m in rewatch" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="updateStatus(m, 'watched')">✅</button>
        <button class="back" @click="updateStatus(m, 'watchlist')">↩️</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>
</template>
