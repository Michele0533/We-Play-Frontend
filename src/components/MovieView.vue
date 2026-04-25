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
/* GENERAL */
body {
  font-family: Arial, sans-serif;
  color: #f1f5f9;
  margin: 0;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

/* NAV */
nav {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 30px;
}

nav button {
  padding: 10px 18px;
  border: none;
  border-radius: 8px;
  background: #1e293b;
  color: white;
  font-size: 16px;
  cursor: pointer;
  transition: 0.2s;
}

nav button:hover {
  background: #3b82f6;
}

/* SEARCH */
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

.search-wrapper input::placeholder {
  color: #94a3b8;
}

/* DROPDOWN */
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
  box-shadow: 0 10px 25px rgba(0,0,0,0.3);
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px;
  cursor: pointer;
  transition: 0.2s;
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
  margin-top: 20px;
}

/* CARD */
.card {
  background: #1e293b;
  border-radius: 12px;
  overflow: hidden;
  padding: 12px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  transition: 0.25s;
  box-shadow: 0 8px 20px rgba(0,0,0,0.25);
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 30px rgba(0,0,0,0.4);
}

/* IMAGE */
.card img {
  width: 100%;
  height: 180px;
  border-radius: 8px;
  object-fit: cover;
}

/* TITLE */
.card h3 {
  font-size: 16px;
  margin: 0;
}

/* DELETE BUTTON */
.card button {
  margin-top: auto;
  padding: 8px;
  border: none;
  border-radius: 6px;
  background: #ef4444;
  color: white;
  cursor: pointer;
  transition: 0.2s;
}

.card button:hover {
  background: #dc2626;
}
</style>
