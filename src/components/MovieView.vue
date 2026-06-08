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
  movies.value = data.results.slice(0, 10)
  showDropdown.value = true
}


// 📥 LOAD + SORT
const loadMovies = async () => {
  const res = await fetch(`${API}/api/movies`)
  const data = await res.json()

  data.sort((a, b) => (a.order ?? 0) - (b.order ?? 0))

  watchlist.value = []
  watched.value = []
  rewatch.value = []

  data.forEach(m => {
    const status = (m.status || 'watchlist').toLowerCase()

    if (status === 'watched') watched.value.push(m)
    else if (status === 'rewatch') rewatch.value.push(m)
    else watchlist.value.push(m)
  })
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
      status: 'watchlist',
      order: 9999
    })
  })

  resetSearch()
  loadMovies()
}


// 🔁 LIVE UPDATE STATUS (FIX)
const updateStatus = async (movie, status) => {

  // 1. sofort aus allen Listen entfernen (LIVE UI FIX)
  watchlist.value = watchlist.value.filter(m => m.id !== movie.id)
  watched.value = watched.value.filter(m => m.id !== movie.id)
  rewatch.value = rewatch.value.filter(m => m.id !== movie.id)

  // 2. Ziel-Liste bestimmen
  let targetList =
    status === 'watched'
      ? watched.value
      : status === 'rewatch'
        ? rewatch.value
        : watchlist.value

  const newOrder = targetList.length

  const updatedMovie = {
    ...movie,
    status,
    order: newOrder
  }

  // 3. sofort UI aktualisieren
  targetList.push(updatedMovie)

  // 4. Backend speichern
  await fetch(`${API}/api/movies/${movie.id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      status,
      order: newOrder
    })
  })
}


// ❌ DELETE
const deleteMovie = async (id) => {
  watchlist.value = watchlist.value.filter(m => m.id !== id)
  watched.value = watched.value.filter(m => m.id !== id)
  rewatch.value = rewatch.value.filter(m => m.id !== id)

  await fetch(`${API}/api/movies/${id}`, {
    method: 'DELETE'
  })
}


// 🔍 RESET SEARCH
const resetSearch = () => {
  search.value = ''
  movies.value = []
  showDropdown.value = false
}


// 🚀 INIT
onMounted(loadMovies)
</script>
