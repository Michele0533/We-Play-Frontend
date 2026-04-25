<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"

const search = ref('')
const games = ref([])
const myGames = ref([])
const showDropdown = ref(false)

// 🔍 Suche
const searchGames = async () => {
  if (!search.value.trim()) {
    games.value = []
    return
  }

  const res = await fetch(
    `https://api.rawg.io/api/games?key=c70c6c39073842fd98878f5f35404ae6&search=${search.value}`
  )

  const data = await res.json()
  games.value = data.results.slice(0, 10)
  showDropdown.value = true
}

// 📥 Liste laden
const loadGames = async () => {
  const res = await fetch(`${API}/api/games`)
  myGames.value = await res.json()
}

// ➕ Spiel hinzufügen
const addGame = async (game) => {
  await fetch(`${API}/api/games`, {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({
      id: game.id,
      name: game.name,
      image: game.background_image
    })
  })

  showDropdown.value = false
  search.value = ''
  games.value = []

  loadGames()
}

// ❌ löschen
const deleteGame = async (id) => {
  await fetch(`${API}/api/games/${id}`, {
    method: 'DELETE'
  })

  loadGames()
}

onMounted(loadGames)
</script>

<template>
  <h2>🎮 Games</h2>

  <div class="search-wrapper">
    <input 
      v-model="search" 
      placeholder="Spiel suchen..." 
      @input="searchGames"
      @focus="showDropdown = true"
    />

    <div v-if="showDropdown && games.length" class="dropdown">
      <div 
        v-for="game in games" 
        :key="game.id" 
        class="dropdown-item"
        @click="addGame(game)"
      >
        <img :src="game.background_image" />
        <span>{{ game.name }}</span>
      </div>
    </div>
  </div>

  <h2>Meine Games</h2>

  <div class="games">
    <div v-for="g in myGames" :key="g.id" class="card">
      <img :src="g.image" />
      <h3>{{ g.name }}</h3>
      <button @click="deleteGame(g.id)">❌</button>
    </div>
  </div>
</template>

<style>
/* GENERAL */
body {
  font-family: Arial, sans-serif;
  background:
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),
    url('./bg.jpg') center/cover no-repeat fixed;

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
