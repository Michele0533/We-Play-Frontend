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
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      id: game.id,
      name: game.name,
      image: game.background_image,
      watching: false
    })
  })

  showDropdown.value = false
  search.value = ''
  games.value = []

  loadGames()
}


// 👀 Gerade am schauen umschalten
const toggleWatching = async (game) => {
  await fetch(`${API}/api/games/${game.id}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      watching: !game.watching
    })
  })

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


  <div 
    v-if="showDropdown && games.length"
    class="dropdown"
  >

    <div
      v-for="game in games"
      :key="game.id"
      class="dropdown-item"
      @click="addGame(game)"
    >

      <img :src="game.background_image" />

      <span>
        {{ game.name }}
      </span>

    </div>

  </div>

</div>



<h2>Meine Games</h2>


<div class="games">

  <div
    v-for="g in myGames"
    :key="g.id"
    class="card"
  >

    <img :src="g.image" />


    <h3>
      {{ g.name }}
    </h3>


    <p 
      v-if="g.watching"
      class="watching"
    >
      👀 Gerade am schauen
    </p>


    <button
      class="watch-btn"
      @click="toggleWatching(g)"
    >
      {{ g.watching ? '✅ Schaue ich gerade' : '👀 Gerade am schauen' }}
    </button>


    <button
      class="delete-btn"
      @click="deleteGame(g.id)"
    >
      ❌ Löschen
    </button>


  </div>

</div>


</template>


<style>

body {
  font-family: Arial, sans-serif;
  background:
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),
    url('/maxresdefault.jpg') center/cover fixed;

  color: #f1f5f9;
  margin: 0;
  padding: 20px;
}


/* SEARCH */

.search-wrapper {
  position: relative;
  width: 500px;
  margin: auto;
}


.search-wrapper input {

  width:100%;
  padding:14px;

  border-radius:10px;
  border:none;

  background:#1e293b;
  color:white;

}


/* DROPDOWN */

.dropdown {

  position:absolute;

  top:110%;
  width:100%;

  background:#1e293b;

  border-radius:10px;

  z-index:10;

}


.dropdown-item {

  display:flex;

  align-items:center;

  gap:12px;

  padding:10px;

  cursor:pointer;

}


.dropdown-item:hover {

  background:#334155;

}


.dropdown-item img {

  width:45px;
  height:45px;

  object-fit:cover;

  border-radius:6px;

}



/* GRID */

.games {

display:grid;

grid-template-columns:
repeat(auto-fill,minmax(220px,1fr));

gap:20px;

margin-top:20px;

}



/* CARD */

.card {

background:#1e293b;

padding:12px;

border-radius:12px;

display:flex;

flex-direction:column;

gap:10px;

box-shadow:
0 8px 20px rgba(0,0,0,.25);

}



.card img {

width:100%;

height:180px;

object-fit:cover;

border-radius:8px;

}



.card h3 {

margin:0;

}



/* WATCHING */

.watching {

color:#22c55e;

font-weight:bold;

}



/* BUTTONS */

button {

padding:8px;

border:none;

border-radius:6px;

cursor:pointer;

color:white;

}


.watch-btn {

background:#3b82f6;

}


.watch-btn:hover {

background:#2563eb;

}


.delete-btn {

background:#ef4444;

}


.delete-btn:hover {

background:#dc2626;

}


</style>
