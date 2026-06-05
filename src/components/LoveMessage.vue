<script setup>
import { ref, reactive, computed } from 'vue'

const show = ref(false)

// 🧠 Lösung (echtes Kreuzwort Raster mit Kreuzungen)
const solution = [
  ['G','E','N','S','H','I','N',null,null,null],
  [null,null,null,null,null,null,'I',null,null,null],
  ['Z','E','L','D','A',null,'F',null,null,null],
  [null,null,null,null,null,null,'E',null,null,null],
  ['L','I','N','K',null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
  ['S','I','L','A','S',null,null,null,null,null],
  ['D','O','K','O','M','I',null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
]

// 🟡 markierte Felder fürs Lösungswort "WIFE"
const solutionWordCells = [
  [0,6],
  [1,6],
  [2,6],
  [3,6],
]

// 🧾 User input
const user = reactive(
  Array.from({ length: 9 }, () =>
    Array.from({ length: 10 }, () => '')
  )
)

// ❌ Fehler-Check pro Feld
const isWrong = (y, x) => {
  if (!solution[y][x]) return false
  const val = user[y][x].toUpperCase()
  return val && val !== solution[y][x]
}

// ✅ Check gesamtes Rätsel
const solved = computed(() => {
  for (let y = 0; y < solution.length; y++) {
    for (let x = 0; x < solution[y].length; x++) {
      if (solution[y][x]) {
        if (user[y][x].toUpperCase() !== solution[y][x]) {
          return false
        }
      }
    }
  }
  return true
})

// 💖 Lösungswort
const checkWord = () => {
  return solutionWordCells
    .map(([y, x]) => user[y][x].toUpperCase())
    .join('') === 'WIFE'
}
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
    <p>Wie im Rätselheft lösen ❤️</p>

    <!-- HINTS wie im Heft -->
    <div class="hints">
      <p>1 → Unser Lieblingsspiel 🎮</p>
      <p>2 → Spielreihe mit Zelda</p>
      <p>3 → Held aus Hyrule</p>
      <p>4 → Name des kleinen Rackers 🐶</p>
      <p>5 → Anime Convention</p>
      <p>↓ Vertikal versteckt: WIFE 💖</p>
    </div>

    <!-- GRID -->
    <div class="grid">
      <div v-for="(row, y) in solution" :key="y" class="row">
        <div v-for="(cell, x) in row" :key="x" class="cell">

          <input
            v-if="cell"
            v-model="user[y][x]"
            maxlength="1"
            :class="{ wrong: isWrong(y,x) }"
          />

          <div v-else class="black"></div>

        </div>
      </div>
    </div>

    <!-- CHECK BUTTON -->
    <button class="check" @click="showResult = true">
      Prüfen ❤️
    </button>

    <!-- RESULT -->
    <div v-if="show && solved" class="win">
      💖 Perfekt gelöst!
      <h1>MY WIFE ❤️</h1>
      <div class="heart">💞💞💞</div>
    </div>

    <div v-if="show && !solved && showResult" class="fail">
      😏 Noch nicht ganz richtig!
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
  background: #ff5c8a;
  border: none;
  border-radius: 30px;
  padding: 14px 16px;
  font-size: 28px;
  color: white;
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
  max-width: 520px;
}

.hints {
  text-align: left;
  margin-bottom: 10px;
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

.wrong {
  background: #ffb3b3;
}

.black {
  width: 100%;
  height: 100%;
  background: black;
}

.check {
  margin-top: 10px;
  padding: 8px 12px;
}

.win {
  margin-top: 10px;
  color: hotpink;
  animation: pop 0.5s ease;
}

.heart {
  font-size: 30px;
  animation: float 1s infinite alternate;
}

.fail {
  color: yellow;
  margin-top: 10px;
}

.close {
  margin-top: 10px;
}

@keyframes pop {
  0% { transform: scale(0.8); }
  100% { transform: scale(1); }
}

@keyframes float {
  0% { transform: translateY(0); }
  100% { transform: translateY(-5px); }
}
</style>
