<script setup>
import { ref, computed } from 'vue'

const show = ref(false)

/* 🧩 ALLE WÖRTER */
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

/* 🧠 GRID */
function createGrid(size = 25) {
  return Array.from({ length: size }, () =>
    Array.from({ length: size }, () => null)
  )
}

/* check fit */
function canPlace(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    let nx = x + (dir === 'across' ? i : 0)
    let ny = y + (dir === 'down' ? i : 0)

    if (!grid[ny] || !grid[ny][nx]) continue
    if (grid[ny][nx] && grid[ny][nx] !== word[i]) return false
  }
  return true
}

/* place word */
function place(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    let nx = x + (dir === 'across' ? i : 0)
    let ny = y + (dir === 'down' ? i : 0)
    grid[ny][nx] = word[i]
  }
}

/* generate */
function generate(words) {
  const grid = createGrid(25)

  words = [...words].sort((a, b) => b.length - a.length)

  const placed = []

  place(grid, words[0], 5, 5, 'across')
  placed.push({ word: words[0], x: 5, y: 5, dir: 'across' })

  for (let w = 1; w < words.length; w++) {
    const word = words[w]
    let ok = false

    for (let p of placed) {
      for (let i = 0; i < p.word.length; i++) {
        for (let j = 0; j < word.length; j++) {
          if (p.word[i] === word[j]) {

            let x = p.x + (p.dir === 'across' ? i : 0) - j
            let y = p.y + (p.dir === 'down' ? i : 0) - j

            let dir = p.dir === 'across' ? 'down' : 'across'

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

/* 🧩 BUILD */
const { grid: solution, placed } = generate(words)

/* 🧠 NUMBERING */
function getNumberMap(grid, placed) {
  const map = {}
  let num = 1

  for (let p of placed) {
    const x = p.x
    const y = p.y

    const beforeX = x - 1
    const beforeY = y - 1

    const isStart =
      !grid[y - (p.dir === 'down' ? 1 : 0)]?.[x - (p.dir === 'across' ? 1 : 0)]

    if (isStart) {
      map[`${y},${x}`] = num++
    }
  }

  return map
}

const numbers = getNumberMap(solution, placed)

/* USER INPUT */
const user = ref(
  solution.map(row =>
    row.map(cell => (cell ? '' : null))
  )
)

/* CHECK */
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

<button class="love-button" @click="show = true">
  ❤️ Rätsel öffnen
</button>

<div v-if="show" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <!-- 🧠 HINTS (NUMMERIERT) -->
    <div class="hints">
      <h3>Hinweise</h3>
      <ul>
        <li>1 → Unser Lieblingsspiel 🎮</li>
        <li>2 → Japanische Animation 🍜</li>
        <li>3 → Gefühl zwischen Menschen ❤️</li>
        <li>4 → Bett... Sport 😏</li>
        <li>5 → Großes PC-Spiel 🎮</li>
        <li>6 → Ort zum Einkaufen 🛒</li>
        <li>7 → Fixierung / „verliebt sein in Idee“ 😏</li>
        <li>8 → Familie 👨‍👩‍👧</li>
        <li>9 → Puls im Körper 💓</li>
        <li>10 → Erwachsener Begriff 😳</li>
        <li>11 → Peak / Gipfel 📈</li>
        <li>12 → Spielzeug 🧸</li>
        <li>13 → Du ❤️</li>
        <li>14 → Ich 😏</li>
        <li>15 → Zelda 🗡️</li>
        <li>16 → Link 🗡️</li>
        <li>17 → Hund 🐶</li>
        <li>18 → Event 🇯🇵</li>
        <li>19 → Fesseln 🔗</li>
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

          <!-- number -->
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

    <button class="check" @click="alert(solved ? '💖 RICHTIG!' : '😏 noch nicht')">
      Prüfen
    </button>

    <div v-if="solved" class="win">
      💖 MY WIFE ❤️
    </div>

    <button @click="show = false">
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
  padding:14px;
  border-radius:30px;
  font-size:24px;
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
  z-index:9998;
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

.hints{
  text-align:left;
  background:#111;
  padding:10px;
  border-radius:10px;
  margin-bottom:15px;
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

.num{
  position:absolute;
  top:1px;
  left:2px;
  font-size:10px;
  color:black;
}
</style>
