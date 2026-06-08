<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"

const search = ref('')
const movies = ref([])

const watchlist = ref([])
const watched = ref([])
const rewatch = ref([])

const showDropdown = ref(false)


// 🔍 SEARCH
const searchMovies = async () => {
  try {
    if (!search.value.trim()) {
      movies.value = []
      return
    }

    const res = await fetch(
      `https://api.themoviedb.org/3/search/multi?api_key=a57b9c639bd4d8f0ec3b12594c9fbdfb&query=${search.value}`
    )

    const data = await res.json()

    movies.value = Array.isArray(data?.results)
      ? data.results.slice(0, 10)
      : []

    showDropdown.value = true

  } catch (err) {
    console.error("SEARCH ERROR:", err)
  }
}


// 📥 LOAD + SORT (SAFE)
const loadMovies = async () => {
  try {
    const res = await fetch(`${API}/api/movies`)
    const data = await res.json()

    console.log("BACKEND:", data)

    watchlist.value = []
    watched.value = []
    rewatch.value = []

    if (!Array.isArray(data)) return

    data.forEach(m => {
      const status = (m?.status || 'watchlist').toLowerCase()

      if (status === 'watched') watched.value.push(m)
      else if (status === 'rewatch') rewatch.value.push(m)
      else watchlist.value.push(m)
    })

  } catch (err) {
    console.error("LOAD ERROR:", err)
  }
}


// ➕ ADD MOVIE
const addMovie = async (movie) => {
  try {
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

  } catch (err) {
    console.error("ADD ERROR:", err)
  }
}


// 🔁 MOVE MOVIE (SAFE PATCH)
const moveMovie = async (movie, status) => {
  try {
    await fetch(`${API}/api/movies/${movie.id}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ status })
    })

    movie.status = status
    loadMovies()

  } catch (err) {
    console.error("MOVE ERROR:", err)
  }
}


// ❌ DELETE
const deleteMovie = async (id) => {
  try {
    await fetch(`${API}/api/movies/${id}`, {
      method: 'DELETE'
    })

    loadMovies()

  } catch (err) {
    console.error("DELETE ERROR:", err)
  }
}


// 🔍 RESET
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}


onMounted(loadMovies)
</script>
