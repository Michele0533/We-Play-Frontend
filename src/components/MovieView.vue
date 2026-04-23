<script setup>
import { ref, onMounted } from 'vue'

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
  <div class="page">
    <h1>🎬 Movies & Serien</h1>

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

    <div class="grid">
      <div v-for="m in myMovies" :key="m.id" class="card">
        <img v-if="m.image" :src="m.image" />
        <h3>{{ m.name }}</h3>
        <button @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>
</template>