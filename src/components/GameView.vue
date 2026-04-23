<script setup>
import { ref, onMounted } from 'vue'

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

// 📥 laden
const loadGames = async () => {
  const res = await fetch(`${API}/api/games`)
  myGames.value = await res.json()
}

// ➕ hinzufügen
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
  <div class="page">
    <h1>🎮 Games</h1>

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

    <div class="grid">
      <div v-for="g in myGames" :key="g.id" class="card">
        <img :src="g.image" />
        <h3>{{ g.name }}</h3>
        <button @click="deleteGame(g.id)">❌</button>
      </div>
    </div>
  </div>
</template>