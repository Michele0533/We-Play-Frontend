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
