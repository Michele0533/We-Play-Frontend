<script setup>
import { ref, reactive, computed } from 'vue'

const show = ref(false)
const reveal = ref(false)

// 🧩 FIXED MANUAL CROSSWORD GRID (clean placement)
const solution = [
  ['C','O','M','P','U','T','E','R','S','P','I','E','L','E',null,null,null,null,null],
  [null,null,null,null,null,null,'A',null,null,null,null,null,null,null,null,null,null,null],
  ['S','U','P','E','R','M','A','R','K','T',null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,'I',null,null,null,null,null,null,null,null,null,null,null],
  ['B','E','T','T','S','P','O','R','T',null,null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,'M',null,null,null,null,null,null,null,null,null,null,null],
  ['A','N','I','M','E',null,'E',null,null,null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['L','I','E','B','E',null,null,null,null,null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['F','A','M','I','L','I','E',null,null,null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['O','B','S','E','S','S','E','D',null,null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['H','Ö','H','E','P','U','N','K','T',null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['G','E','N','S','H','I','N',null,null,null,null,null,null,null,null,null,null,null],
  ['Z','E','L','D','A',null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['S','I','L','A','S',null,null,null,null,null,null,null,null,null,null,null,null,null],
]

// input grid
const user = reactive(
  Array.from({ length: 19 }, () =>
    Array.from({ length: 19 }, () => '')
  )
)

// check correctness
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

// final message
const finalText = computed(() => {
  return solved.value ? "💖 MY WIFE ❤️ WILL YOU MARRY ME?" : ""
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
    <p>Löse alle Begriffe ❤️</p>

    <!-- HINTS (Heftstyle) -->
    <div class="hints">
      <p>1→ Computerspiele</p>
      <p>2→ Supermarkt</p>
      <p>3→ Bettsport</p>
      <p>4→ Anime</p>
      <p>5→ Liebe</p>
      <p>6→ Familie</p>
      <p>7→ Obsesssed</p>
      <p>8→ Höhepunkt</p>
      <p>9→ Spielzeug</p>
      <p>10→ Herzschlag</p>
      <p>11→ Genshin</p>
      <p>12→ Zelda</p>
      <p>13→ Link</p>
      <p>14→ Silas 🐶</p>
      <p>15→ Dokomi</p>
      <p>16→ Anketten</p>
      <p>17→ Wife</p>
      <p>18→ Husband</p>
    </div>

    <!-- GRID -->
    <div class="grid">
      <div v-for="(row,y) in solution" :key="y" class="row">
        <div v-for="(cell,x) in row" :key="x" class="cell">

          <input
            v-if="cell"
            v-model="user[y][x]"
            maxlength="1"
          />

          <div v-else class="black"></div>

        </div>
      </div>
    </div>

    <!-- CHECK -->
    <button class="check" @click="reveal = true">
      Prüfen ❤️
    </button>

    <!-- REVEAL -->
    <div v-if="reveal" class="result">

      <div v-if="solved" class="win">
        💖 Richtig gelöst!
        <h1 class="final">{{ finalText }}</h1>
        <div class="hearts">💞💞💞</div>
      </div>

      <div v-else class="fail">
        😏 Noch nicht alles richtig…
      </div>

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
  border-radius: 30px;
  padding: 14px;
  font-size: 28px;
  border: none;
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
  background: #777;
  padding: 20px;
  border-radius: 20px;
  max-width: 900px;
  text-align: center;
}

.hints {
  text-align: left;
  font-size: 13px;
  margin-bottom: 10px;
}

.grid {
  display: inline-block;
}

.row {
  display: flex;
}

.cell {
  width: 22px;
  height: 22px;
  border: 1px solid #ddd;
}

input {
  width: 100%;
  height: 100%;
  text-align: center;
  font-size: 10px;
  border: none;
}

.black {
  background: black;
  width: 100%;
  height: 100%;
}

.check {
  margin-top: 10px;
  padding: 8px;
}

.result {
  margin-top: 10px;
}

.win {
  color: hotpink;
  animation: pop 0.6s ease;
}

.hearts {
  font-size: 26px;
  animation: float 1s infinite alternate;
}

.fail {
  color: yellow;
}

.final {
  margin-top: 10px;
}

@keyframes pop {
  0% { transform: scale(0.7); }
  100% { transform: scale(1); }
}

@keyframes float {
  0% { transform: translateY(0); }
  100% { transform: translateY(-6px); }
}
</style>
