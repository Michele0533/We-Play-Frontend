<script setup>
import { ref } from 'vue'
import GameView from './components/GameView.vue'
import MovieView from './components/MovieView.vue'
import LoveMessage from './components/LoveMessage.vue'
import NewButton from './components/NewButton.vue'
  import GenshinDashboard from './components/GenshinDashboard.vue'

const page = ref('games')
</script>

<template>
  <h1>WePlay 🎮</h1>

  <nav>
    <button @click="page = 'games'">🎮 Games</button>
    <button @click="page = 'movies'">🎬 Movies</button>
    <button @click="page = 'genshin'">⚡ Genshin</button>
  </nav>

  <LoveMessage />

  <NewButton />

  <GameView v-if="page === 'games'" />
  <MovieView v-if="page === 'movies'" />
  <GenshinDashboard v-if="page === 'genshin'" />
</template>
