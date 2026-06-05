<script setup>
import { ref, reactive, computed } from 'vue'

/* =========================
   POPUP STATE (ORIGINAL)
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
  "SPIELZEUG",
  "WIFE",
  "HUSBAND",
  "ZELDA",
  "LINK",
  "SILAS",
  "DOKOMI",
  "ANKETTEN",
   "Wolf",
   "Stöhnen"
]

/* =========================
   CLUES (DEINE HINTS)
========================= */
const clueMap = {
  GENSHIN: "Unser Lieblingsspiel 🎮",
  ANIME: "Seriengenre aus Japan 🍜",
  LIEBE: "Starkes Gefühl zwischen dir und mir❤️",
  BETTSPORT: "😏 im Bett mit dir honey",
  COMPUTERSPIELE: "Was wir jeden tag machen",
  SUPERMARKT: "Pingolf",
  OBSESSED: "das was ich mit dir bin",
  FAMILIE: "Du, ich und eine Mini von dir",
  HERZSCHLAG: "Puls im Körper 💓",
  ORGASMUS: "Höhepunkt 😳",
  SPIELZEUG: "Dinge die man ins bett nehmen kann^^",
  WIFE: "du ❤️",
  HUSBAND: "ich 😏",
  ZELDA: "Deinliebligns spiel",
  LINK: "Held aus Zelda 🗡️",
  SILAS: "kleiner Racker 🐶",
  DOKOMI: "Anime Convention 🇯🇵",
  ANKETTEN: "unser insider",
   Wolf: "dein lieblingstier",
   Stöhnen: "Dein tolles atmen, wenn ich an dir rumspiele"
}

/* =========================
   GRID ENGINE
========================= */
const SIZE = 30

function emptyGrid() {
  return Array.from({ length: SIZE }, () =>
    Array.from({ length: SIZE }, () => null)
  )
}

function canPlace(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)

    if (!grid[ny] || nx < 0 || ny < 0 || nx >= SIZE || ny >= SIZE) return false

    const cell = grid[ny][nx]
    if (cell && cell !== word[i]) return false
  }
  return true
}

function place(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)
    grid[ny][nx] = word[i]
  }
}

/* =========================
   GENERATE (retry safe)
========================= */
function generate(words) {
  const grid = emptyGrid()
  const placed = []

  const sorted = [...words].sort((a, b) => b.length - a.length)

  place(grid, sorted[0], 10, 10, 'across')
  placed.push({ word: sorted[0], x: 10, y: 10, dir: 'across' })

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
              place(grid, word, x, y, dir)
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
   NUMBERS + ARROWS
========================= */
const startMap = {}
let counter = 1

for (const p of placed) {
  const key = `${p.y},${p.x}`

  if (!startMap[key]) {
    startMap[key] = {
      num: counter++,
      arrows: []
    }
  }

  startMap[key].arrows.push(p.dir === 'across' ? '→' : '↓')
}

/* =========================
   CLUES
========================= */
const clues = placed.map((p, i) => {
  return {
    num: i + 1,
    dir: p.dir === 'across' ? '→' : '↓',
    text: clueMap[p.word] || p.word
  }
})

/* =========================
   USER GRID
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

<!-- BUTTON -->
<button class="love-button" @click="showMessage = true">
  ❤️ Nachricht
</button>

<!-- POPUP -->
<div v-if="showMessage" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <!-- CLUES -->
    <div class="hints">
      <h3>Hinweise</h3>
      <ul>
        <li v-for="c in clues" :key="c.num">
          {{ c.num }} {{ c.dir }} {{ c.text }}
        </li>
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

          <div v-if="startMap[`${y},${x}`]" class="meta">
            <span class="num">
              {{ startMap[`${y},${x}`].num }}
            </span>
            <span class="arrow">
              {{ startMap[`${y},${x}`].arrows.join('') }}
            </span>
          </div>

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

.overlay{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,0.7);
  display:flex;
  justify-content:center;
  align-items:center;
}

.popup{
  background:#222;
  color:white;
  padding:20px;
  border-radius:16px;
  width:min(900px,95vw);
  max-height:90vh;
  overflow:auto;
}

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

.meta{
  position:absolute;
  top:1px;
  left:2px;
  font-size:9px;
  display:flex;
  gap:2px;
  color:black;
}

.num{
  font-weight:bold;
}

.arrow{
  opacity:0.8;
}

.win{
  color:#ff4fa3;
  font-size:22px;
}

.fail{
  color:yellow;
}
</style>
