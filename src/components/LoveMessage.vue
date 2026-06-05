<script setup>
import { ref, reactive, computed } from 'vue'

/* =========================
   BUTTON STATE (OLD STYLE)
========================= */
const showMessage = ref(false)
const reveal = ref(false)

/* =========================
   WORDS
========================= */
const words = [
  "GENSHIN",
  "ANIME",
  "LIEBE",
  "BETTSPORT",
  "COMPUTERSPIELE",
  "SUPERMARKT",
  "OBSESSED",
  "FAMILIE",
  "HERZSCHLAG",
  "ORGASMUS",
  "HÖHEPUNKT",
  "SPIELZEUG",
  "WIFE",
  "HUSBAND",
  "ZELDA",
  "LINK",
  "SILAS",
  "DOKOMI",
  "ANKETTEN"
]

/* =========================
   CROSSWORD ENGINE
========================= */
function createGrid(size = 25) {
  return Array.from({ length: size }, () =>
    Array.from({ length: size }, () => null)
  )
}

function canPlace(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)

    if (!grid[ny] || grid[ny][nx] === undefined) return false

    const cell = grid[ny][nx]
    if (cell && cell !== word[i]) return false
  }
  return true
}

function placeWord(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)
    grid[ny][nx] = word[i]
  }
}

function generate(words) {
  const grid = createGrid(25)
  const placed = []

  const sorted = [...words].sort((a, b) => b.length - a.length)

  placeWord(grid, sorted[0], 5, 5, 'across')
  placed.push({ word: sorted[0], x: 5, y: 5, dir: 'across' })

  for (let w = 1; w < sorted.length; w++) {
    const word = sorted[w]
    let ok = false

    for (const p of placed) {
      for (let i = 0; i < p.word.length; i++) {
        for (let j = 0; j < word.length; j++) {
          if (p.word[i] === word[j]) {
            const x = p.x + (p.dir === 'across' ? i : 0) - j
            const y = p.y + (p.dir === 'down' ? i : 0) - j
            const dir = p.dir === 'across' ? 'down' : 'across'

            if (canPlace(grid, word, x, y, dir)) {
              placeWord(grid, word, x, y, dir)
              placed.push({ word, x, y, dir })
              ok = true
              break
            }
          }
        }
        if (ok) break
      }
      if (ok) break
    }
  }

  return { grid, placed }
}

const { grid: solution, placed } = generate(words)

/* =========================
   NUMBERING (1→ style)
========================= */
function buildNumbers(grid, placed) {
  const map = {}
  let n = 1

  for (const p of placed) {
    const startX = p.x
    const startY = p.y

    const left = grid[startY]?.[startX - 1]
    const top = grid[startY - 1]?.[startX]

    if (!left && !top) {
      map[`${startY},${startX}`] = n++
    }
  }

  return map
}

const numbers = buildNumbers(solution, placed)

/* =========================
   USER INPUT GRID
========================= */
const user = ref(
  solution.map(row =>
    row.map(cell => (cell ? '' : null))
  )
)

/* =========================
   CHECK
========================= */
const solved = computed(() => {
  for (let y = 0; y < solution.length; y++) {
    for (let x = 0; x < solution[y].length; x++) {
      if (solution[y][x]) {
        if (user.value[y][x].toUpperCase() !== solution[y][x]) {
          return false
        }
      }
    }
  }
  return true
})
</script>

<template>

<!-- ✅ ORIGINAL BUTTON (RESTORED) -->
<button class="love-button" @click="showMessage = true">
  ❤️ Nachricht
</button>

<!-- POPUP -->
<div v-if="showMessage" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <!-- HINTS -->
    <div class="hints">
      <h3>Hinweise</h3>
      <ul>
        <li>🎮 Unser Lieblingsspiel</li>
        <li>🍜 Anime</li>
        <li>❤️ Liebe</li>
        <li>👨‍👩‍👧 Familie</li>
        <li>🛒 Supermarkt</li>
        <li>🛏️ Bettsport</li>
        <li>📈 Höhepunkt</li>
        <li>💓 Herzschlag</li>
        <li>🧸 Spielzeug</li>
        <li>😏 Obsesssed</li>
        <li>🎮 Genshin / Zelda / Link</li>
      </ul>
    </div>

    <!-- GRID -->
    <div class="grid">
      <div v-for="(row,y) in solution" :key="y" class="row">
        <div
          v-for="(cell,x) in row"
          :key="x"
          class="cell"
          :class="{ black: !cell }"
        >
          <span v-if="numbers[`${y},${x}`]" class="num">
            {{ numbers[`${y},${x}`] }}
          </span>

          <input
            v-if="cell"
            v-model="user[y][x]"
            maxlength="1"
          />
        </div>
      </div>
    </div>

    <!-- CHECK -->
    <button class="check" @click="reveal = true">
      Prüfen ❤️
    </button>

    <!-- RESULT -->
    <div v-if="reveal">
      <div v-if="solved" class="win">
        💖 MY WIFE ❤️
      </div>
      <div v-else class="fail">
        😏 Noch nicht alles richtig
      </div>
    </div>

    <!-- CLOSE -->
    <button @click="showMessage = false">
      Schließen
    </button>

  </div>
</div>

</template>

<style scoped>

/* BUTTON (OLD SAFE STYLE) */
.love-button{
  position:fixed;
  bottom:20px;
  right:20px;
  background:#ff5c8a;
  border:none;
  padding:14px 18px;
  border-radius:30px;
  font-size:22px;
  color:white;
  z-index:9999;
}

/* OVERLAY */
.overlay{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,0.7);
  display:flex;
  justify-content:center;
  align-items:center;
  z-index:9998;
}

/* POPUP */
.popup{
  background:#222;
  color:white;
  padding:20px;
  border-radius:16px;
  width:min(900px,95vw);
  max-height:90vh;
  overflow:auto;
}

/* GRID */
.grid{
  display:flex;
  flex-direction:column;
  align-items:center;
}

.row{
  display:flex;
}

.cell{
  width:30px;
  height:30px;
  border:1px solid #444;
  position:relative;
  background:white;
}

.cell.black{
  background:black;
}

input{
  width:100%;
  height:100%;
  border:none;
  text-align:center;
  font-weight:bold;
}

.num{
  position:absolute;
  top:1px;
  left:2px;
  font-size:10px;
  color:black;
}

/* RESULT */
.win{
  color:#ff4fa3;
  font-size:22px;
}

.fail{
  color:yellow;
}
</style>
