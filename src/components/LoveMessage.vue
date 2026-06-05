<script setup>
import { ref, computed, watch } from 'vue'

/* =========================
   POPUP STATE
========================= */
const showMessage = ref(false)
const reveal = ref(false)

/* FREEZE BACKGROUND */
watch(showMessage, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
  document.documentElement.style.overflow = val ? 'hidden' : ''
})

/* =========================
   WORDS (UNVERÄNDERT)
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
   CLUE MAP (UNVERÄNDERT)
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
  const w = (word || "").toUpperCase()

  for (let i = 0; i < w.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)

    if (!grid[ny] || nx < 0 || ny < 0 || nx >= SIZE || ny >= SIZE) return false

    const cell = grid[ny][nx]
    if (cell && cell !== w[i]) return false
  }
  return true
}

function place(grid, word, x, y, dir) {
  const w = (word || "").toUpperCase()

  for (let i = 0; i < w.length; i++) {
    const nx = x + (dir === 'across' ? i : 0)
    const ny = y + (dir === 'down' ? i : 0)
    grid[ny][nx] = w[i]
  }
}

function generate(words) {
  const grid = emptyGrid()
  const placed = []

  const sorted = [...words].sort((a, b) => b.length - a.length)

  place(grid, sorted[0], 10, 10, 'across')
  placed.push({ word: sorted[0].toUpperCase(), x: 10, y: 10, dir: 'across' })

  for (let w = 1; w < sorted.length; w++) {
    const word = (sorted[w] || "").toUpperCase()
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
   START MAP
========================= */
const startMap = {}
const startLetters = {}
let counter = 1

for (const p of placed) {
  const key = `${p.y},${p.x}`

  if (!startMap[key]) {
    startMap[key] = { num: counter++, arrows: [] }
    startLetters[key] = p.word[0]
  }

  startMap[key].arrows.push(p.dir === 'across' ? '→' : '↓')
}

/* =========================
   CLUES
========================= */
const clues = placed.map((p, i) => ({
  num: i + 1,
  dir: p.dir === 'across' ? '→' : '↓',
  text: clueMap[p.word] || clueMap[p.word.toUpperCase()] || p.word
}))

/* =========================
   USER GRID
========================= */
const user = ref(
  solution.map(row =>
    row.map(cell => (cell ? '' : null))
  )
)

/* =========================
   INPUT
========================= */
function moveNext(event, y, x) {
  const key = event.key
  if (!/^[a-zA-Z]$/.test(key)) return
  user.value[y][x] = key.toUpperCase()
}

/* =========================
   SOLVED (FIXED)
========================= */
const solved = computed(() => {
  for (let y = 0; y < solution.length; y++) {
    for (let x = 0; x < solution[y].length; x++) {
      const correct = solution[y][x]

      if (correct) {
        const input = (user.value[y][x] || "").toUpperCase()
        const target = (correct || "").toUpperCase()

        if (input !== target) return false
      }
    }
  }
  return true
})

/* =========================
   COLORS (FIXED)
========================= */
function isWrong(y, x) {
  if (!reveal.value) return false
  const correct = solution[y][x]
  if (!correct) return false

  return (user.value[y][x] || "").toUpperCase() !== correct
}

function isCorrect(y, x) {
  if (!reveal.value) return false
  const correct = solution[y][x]
  if (!correct) return false

  return (user.value[y][x] || "").toUpperCase() === correct
}
</script>

<template>
<button class="love-button" @click="showMessage = true">
  ❤️ Nachricht
</button>

<div v-if="showMessage" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <div class="hints">
      <ul>
        <li v-for="c in clues" :key="c.num">
          {{ c.num }} {{ c.dir }} {{ c.text }}
        </li>
      </ul>
    </div>

    <div class="grid">
      <div v-for="(row,y) in solution" :key="y" class="row">

        <div
          v-for="(cell,x) in row"
          :key="x"
          class="cell"
          :class="{ black: !cell }"
        >

          <input
            v-if="cell"
            v-model="user[y][x]"
            maxlength="1"
            @keydown="moveNext($event, y, x)"
            :class="{
              wrong: isWrong(y, x),
              correct: isCorrect(y, x)
            }"
          />

        </div>

      </div>
    </div>

    <button @click="reveal = true">Prüfen ❤️</button>

    <div v-if="reveal">
      <div v-if="solved" class="end">💖 I LOVE YOU 💖</div>
      <div v-else class="fail">😏 Noch nicht alles richtig</div>
    </div>

    <button @click="showMessage = false">Schließen</button>

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
  color:white;
  font-size:20px;
}

.overlay{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,0.6);
  display:flex;
  justify-content:center;
  align-items:center;
}

.popup{
  background:#1f1f1f;
  color:white;
  padding:20px;
  border-radius:16px;
}

.cell{
  width:30px;
  height:30px;
  border:1px solid #444;
  background:white;
}

.cell.black{
  background:black;
}

input{
  width:100%;
  height:100%;
  text-align:center;
  font-weight:bold;
  border:none;
}

input.wrong{
  background:red !important;
  color:white;
}

input.correct{
  background:green !important;
  color:white;
}

.end{
  margin-top:20px;
  color:#ff4fa3;
  text-align:center;
}

.fail{
  margin-top:20px;
  color:yellow;
}
</style>
