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
  if (!search.value.trim()) {
    movies.value = []
    return
  }

  const res = await fetch(
    `https://api.themoviedb.org/3/search/multi?api_key=a57b9c639bd4d8f0ec3b12594c9fbdfb&query=${search.value}`
  )

  const data = await res.json()
  movies.value = data.results || []
  showDropdown.value = true
}


// 📥 LOAD (SAFE VERSION)
const loadMovies = async () => {
  try {
    const res = await fetch(`${API}/api/movies`)
    const data = await res.json()

    console.log("BACKEND DATA:", data)

    watchlist.value = []
    watched.value = []
    rewatch.value = []

    data.forEach(m => {
      const status = (m.status || 'watchlist')

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


// 🔁 STATUS UPDATE (SIMPLE + SAFE)
const updateStatus = async (movie, status) => {
  await fetch(`${API}/api/movies/${movie.id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ status })
  })

  loadMovies()
}


// ❌ DELETE
const deleteMovie = async (id) => {
  await fetch(`${API}/api/movies/${id}`, {
    method: 'DELETE'
  })

  loadMovies()
}


// 🔍 RESET
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}


// 🚀 INIT
onMounted(() => {
  console.log("APP START")
  loadMovies()
})
</script>
