<script setup>
import { ref } from 'vue'

import GameView from './components/GameView.vue'
import MovieView from './components/MovieView.vue'
import LoveMessage from './components/LoveMessage.vue'
import NewButton from './components/NewButton.vue'
import GenshinDashboard from './components/GenshinDashboard.vue'

const page = ref('games')
const showPopup = ref(true)
</script>

<template>
  <div v-if="showPopup" class="popup-overlay">
    <div class="popup">
      <h2>🎁 Hinweis</h2>
      <p><strong>GUTSCHEIN NOCH NICHT EINGELÖST</strong></p>
      <button @click="showPopup = false">OK</button>
    </div>
  </div>

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

<style scoped>
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.popup {
  background: white;
  padding: 30px;
  border-radius: 12px;
  text-align: center;
  min-width: 320px;
  box-shadow: 0 10px 25px rgba(0,0,0,.3);
}

.popup button {
  margin-top: 20px;
  padding: 10px 20px;
  cursor: pointer;
}
</style>
