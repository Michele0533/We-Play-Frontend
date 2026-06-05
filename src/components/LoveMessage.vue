<script setup>
import { ref, reactive, computed } from 'vue'

const show = ref(false)

// Lösung (8x8 Grid)
const solution = [
  ['G','E','N','S','H','I','N',null],
  [null,null,null,null,null,null,null,null],
  ['Z','E','L','D','A',null,null,null],
  [null,null,null,null,null,null,null,null],
  ['L','I','N','K',null,null,null,null],
  [null,null,null,null,null,null,null,null],
  ['S','I','L','A','S',null,null,null],
  ['D','O','K','O','M','I',null,null],
]

// User input
const user = reactive(
  Array.from({ length: 8 }, () =>
    Array.from({ length: 8 }, () => '')
  )
)

// Check
const solved = computed(() => {
  for (let y = 0; y < 8; y++) {
    for (let x = 0; x < 8; x++) {
      if (solution[y][x]) {
        if (user[y][x].toUpperCase() !== solution[y][x]) {
          return false
        }
      }
    }
  }
  return true
})
</script>

<template>

<!-- BUTTON -->
<button class="love-button" @click="show = true">
  ❤️ Nachricht
</button>

<!-- POPUP -->
<div v-if="show" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>
    <p>Löse die Hinweise ❤️</p>

    <!-- HINTS -->
    <div class="hints">
      <p>1. Unser Lieblingsspiel 🎮</p>
      <p>2. Spielreihe mit Zelda</p>
      <p>3. Held aus Hyrule</p>
      <p>4. Name des kleinen Rackers 🐶</p>
      <p>5. Anime Convention in Düsseldorf</p>
      <p>6. Englisches Wort für Ehefrau (verborgen im Rätsel)</p>
    </div>

    <!-- GRID -->
    <div class="grid">
      <div v-for="(row, y) in solution" :key="y" class="row">
        <div v-for="(cell, x) in row" :key="x" class="cell">

          <input
            v-if="cell"
            v-model="user[y][x]"
            maxlength="1"
          />

          <div v-else class="black"></div>

        </div>
      </div>
    </div>

    <!-- RESULT -->
    <div v-if="solved" class="win">
      💖 Richtig gelöst!
      <h1>MY WIFE ❤️</h1>
    </div>

    <button class="close" @click="show = false">
      Schließen
    </button>

  </div>
</div>

</template>

<style scoped>
.love-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 14px 18px;
  border: none;
  border-radius: 30px;
  background: #ff5c8a;
  color: white;
  font-size: 28px;
  cursor: pointer;
}

.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.6);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup {
  background: #888;
  padding: 20px;
  border-radius: 20px;
  text-align: center;
  max-width: 500px;
}

.hints {
  text-align: left;
  margin-bottom: 15px;
}

.grid {
  display: inline-block;
}

.row {
  display: flex;
}

.cell {
  width: 32px;
  height: 32px;
  border: 1px solid #ddd;
}

input {
  width: 100%;
  height: 100%;
  text-align: center;
  border: none;
  font-weight: bold;
}

.black {
  width: 100%;
  height: 100%;
  background: black;
}

.win {
  margin-top: 15px;
  color: hotpink;
}

.close {
  margin-top: 15px;
}
</style>
