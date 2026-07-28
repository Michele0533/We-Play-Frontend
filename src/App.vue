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
  <!-- Popup -->
  <div v-if="showPopup" class="popup-overlay">
    <div class="popup">
      <h2>🎁 Gutschein</h2>
      <p><strong>GUTSCHEIN NOCH NICHT EINGELÖST!</strong></p>
      <button @click="showPopup = false">Verstanden ❤️</button>
    </div>
  </div>

  <!-- Hauptinhalt -->
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
/* Hintergrund des Popups */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.65);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

/* Popup-Fenster */
.popup {
  background: #ffffff;
  color: #222;
  padding: 35px;
  border-radius: 16px;
  text-align: center;
  min-width: 360px;
  max-width: 90%;
  box-shadow: 0 12px 35px rgba(0, 0, 0, 0.4);
  animation: popup 0.3s ease-out;
}

.popup h2 {
  margin-top: 0;
  margin-bottom: 15px;
  color: #d32f2f;
  font-size: 2rem;
}

.popup p {
  font-size: 1.2rem;
  color: #333;
  margin-bottom: 25px;
}

.popup button {
  background: #1976d2;
  color: white;
  border: none;
  padding: 12px 30px;
  font-size: 1rem;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.popup button:hover {
  background: #1565c0;
  transform: scale(1.05);
}

.popup button:active {
  transform: scale(0.98);
}

/* Einblendanimation */
@keyframes popup {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
