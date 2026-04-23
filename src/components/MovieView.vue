<script setup>
import { ref, onMounted } from 'vue'

const API = "https://DEIN-BACKEND.onrender.com"

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

// 📥 Liste laden
const loadMovies = async () => {
  const res = await fetch(`${API}/api/movies`)
  myMovies.value = await res.json()
}

// ➕ hinzufügen
const addMovie = async (movie) => {
  await fetch(`${API}/api/movies`, {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
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

  <h2>Meine Liste</h2>

  <div class="games">
    <div v-for="m in myMovies" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>
      <button @click="deleteMovie(m.id)">❌</button>
    </div>
  </div>
</template>

<style>
.search-wrapper {
  position: relative;
  width: 500px;
  margin: 0 auto 20px auto;
}

.search-wrapper input {
  width: 100%;
  padding: 12px 14px;
  font-size: 18px;
  border: 1px solid #bbb;
  border-radius: 6px;
}

.dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  border: 1px solid #ccc;
  max-height: 250px;
  overflow-y: auto;
  z-index: 10;
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 8px;
  cursor: pointer;
}

.dropdown-item:hover {
  background: #f0f0f0;
}

.dropdown-item img {
  width: 40px;
  height: 40px;
  object-fit: cover;
}

.games {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
}

.card {
  border: 1px solid #ccc;
  padding: 10px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  display: block;
}
</style>