<script setup>
import { ref } from 'vue'
import GameView from './components/GameView.vue'
import MovieView from './components/MovieView.vue'

const page = ref('games')
</script>

<template>
  <h1>WePlay 🎮</h1>

  <nav>
    <button @click="page = 'games'">🎮 Games</button>
    <button @click="page = 'movies'">🎬 Movies</button>
  </nav>

  <GameView v-if="page === 'games'" />
  <MovieView v-if="page === 'movies'" />
</template>