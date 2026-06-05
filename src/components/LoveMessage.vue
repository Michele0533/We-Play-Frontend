<script setup>
import { ref, computed, watch } from "vue"

/* ========================= POPUP STATE ========================= */
const showMessage = ref(false)
const reveal = ref(false)

watch(showMessage, (val) => {
  document.body.style.overflow = val ? "hidden" : ""
  document.documentElement.style.overflow = val ? "hidden" : ""
})

/* ========================= WORDS ========================= */
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
  "WOLF",
  "STÖHNEN"
]

/* ========================= CLUE MAP ========================= */
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
  ZELDA: "Dein lieblings spiel",
  LINK: "Held aus Zelda 🗡️",
  SILAS: "kleiner Racker 🐶",
  DOKOMI: "Anime Convention 🇯🇵",
  ANKETTEN: "unser insider",
  WOLF: "dein lieblingstier",
  STÖHNEN: "Dein tolles atmen 😏"
}

/* ========================= NORMALIZE ========================= */
const normalize = (str) =>
  (str || "")
    .toUpperCase()
    .normalize("NFD")
    .replace(/[\u0300-\u036f]/g, "")
    .replace(/ß/g, "SS")
    .replace(/Ä/g, "A")
    .replace(/Ö/g, "O")
    .replace(/Ü/g, "U")

/* ========================= GRID ENGINE ========================= */
const SIZE = 30

function emptyGrid() {
  return Array.from({ length: SIZE }, () =>
    Array.from({ length: SIZE }, () => null)
  )
}

function canPlace(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === "across" ? i : 0)
    const ny = y + (dir === "down" ? i : 0)

    if (nx < 0 || ny < 0 || nx >= SIZE || ny >= SIZE) return false

    const cell = grid[ny]?.[nx]
    if (cell && cell !== word[i]) return false
  }
  return true
}

function place(grid, word, x, y, dir) {
  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === "across" ? i : 0)
    const ny = y + (dir === "down" ? i : 0)
    grid[ny][nx] = word[i]
  }
}

function generate(words) {
  const grid = emptyGrid()
  const placed = []

  const sorted = [...words].sort((a, b) => b.length - a.length)

  const first = sorted[0]
  const startX = 10
  const startY = 10

  place(grid, first, startX, startY, "across")
  placed.push({ word: first, x: startX, y: startY, dir: "across" })

  for (let w = 1; w < sorted.length; w++) {
    const word = sorted[w]
    let ok = false

    for (const p of placed) {
      for (let i = 0; i < p.word.length; i++) {
        for (let j = 0; j < word.length; j++) {
          if (p.word[i] !== word[j]) continue

          const x =
            p.x + (p.dir === "across" ? i : 0) -
            (p.dir === "down" ? 0 : j)

          const y =
            p.y + (p.dir === "down" ? i : 0) -
            (p.dir === "across" ? 0 : j)

          const dir = p.dir === "across" ? "down" : "across"

          if (canPlace(grid, word, x, y, dir)) {
            place(grid, word, x, y, dir)
            placed.push({ word, x, y, dir })
            ok = true
            break
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

/* ========================= START MAP ========================= */
const startMap = {}
const startLetters = {}

let counter = 1

for (const p of placed) {
  const key = `${p.y},${p.x}`

  if (!startMap[key]) {
    startMap[key] = { num: counter++, arrows: [] }
    startLetters[key] = p.word[0]
  }

  startMap[key].arrows.push(p.dir === "across" ? "→" : "↓")
}

/* ========================= USER GRID ========================= */
const user = ref(
  solution.map((row) =>
    row.map((cell) => (cell ? "" : null))
  )
)

/* ========================= WORD CHECK ========================= */
const completedWords = ref(new Set())

function isWordComplete(wordObj) {
  const { word, x, y, dir } = wordObj

  for (let i = 0; i < word.length; i++) {
    const nx = x + (dir === "across" ? i : 0)
    const ny = y + (dir === "down" ? i : 0)

    const correct = solution[ny]?.[nx]
    if (!correct) return false

    if (
      normalize(user.value[ny][nx]) !== normalize(correct)
    ) return false
  }

  return true
}

function isPartOfCompletedWord(y, x) {
  return placed.some((p) => {
    const key = p.word + p.x + p.y
    if (!completedWords.value.has(key)) return false

    for (let i = 0; i < p.word.length; i++) {
      const nx = p.x + (p.dir === "across" ? i : 0)
      const ny = p.y + (p.dir === "down" ? i : 0)

      if (nx === x && ny === y) return true
    }

    return false
  })
}

watch(
  user,
  () => {
    placed.forEach((p) => {
      const key = p.word + p.x + p.y

      if (isWordComplete(p)) {
        completedWords.value.add(key)
      } else {
        completedWords.value.delete(key)
      }
    })
  },
  { deep: true }
)

/* ========================= SOLVED ========================= */
const solved = computed(() => {
  for (let y = 0; y < solution.length; y++) {
    for (let x = 0; x < solution[y].length; x++) {
      const correct = solution[y][x]
      if (correct) {
        if (
          normalize(user.value[y][x]) !== normalize(correct)
        ) return false
      }
    }
  }
  return true
})

watch(solved, (val) => {
  if (val) confettiBurst()
})

/* ========================= CONFETTI ========================= */
function confettiBurst() {
  for (let i = 0; i < 40; i++) {
    const div = document.createElement("div")

    div.style.position = "fixed"
    div.style.left = Math.random() * 100 + "vw"
    div.style.top = "-10px"
    div.style.width = "8px"
    div.style.height = "8px"
    div.style.background =
      ["#ff4d6d", "#ffd166", "#06d6a0"][Math.floor(Math.random() * 3)]
    div.style.zIndex = 99999
    div.style.animation = "fall 2s linear"

    document.body.appendChild(div)
    setTimeout(() => div.remove(), 2000)
  }
}

/* ========================= INPUT ========================= */
function moveNext(event, y, x) {
  const key = event.key

  if (key === "Backspace") {
    user.value[y][x] = ""
    return
  }

  if (!/^[a-zA-ZÄÖÜäöüß]$/.test(key)) return

  user.value[y][x] = key.toUpperCase()
}

/* ========================= CHECK COLOR ========================= */
function isWrong(y, x) {
  if (!reveal.value) return false
  const correct = solution[y][x]
  if (!correct) return false

  return (
    normalize(user.value[y][x]) !== normalize(correct)
  )
}

function isCorrect(y, x) {
  if (!reveal.value) return false
  const correct = solution[y][x]
  if (!correct) return false

  return (
    normalize(user.value[y][x]) === normalize(correct)
  )
}
</script>

<template>
  <button class="love-button" @click="showMessage = true">
    ❤️ Nachricht
  </button>

  <div v-if="showMessage" class="overlay">
    <div class="popup">
      <h2>🧩 Kreuzworträtsel</h2>

      <div class="grid">
        <div v-for="(row, y) in solution" :key="y" class="row">
          <div
            v-for="(cell, x) in row"
            :key="x"
            class="cell"
            :class="{ black: !cell }"
          >
            <div v-if="startMap[`${y},${x}`]" class="meta">
              <span class="num">
                {{ startMap[`${y},${x}`].num }}
              </span>
              <span class="arrow">
                {{ startMap[`${y},${x}`].arrows.join("") }}
              </span>
            </div>

            <input
              v-if="cell"
              v-model="user[y][x]"
              maxlength="1"
              @keydown="moveNext($event, y, x)"
              :placeholder="startLetters[`${y},${x}`] || ''"
              :class="{
                wrong: isWrong(y, x),
                correct: isCorrect(y, x),
                worddone: isPartOfCompletedWord(y, x)
              }"
            />
          </div>
        </div>
      </div>

      <button class="check" @click="reveal = true">
        Prüfen ❤️
      </button>

      <div v-if="reveal">
        <div v-if="solved" class="end">
          💖 I LOVE YOU 💖
        </div>
        <div v-else class="fail">
          😏 Noch nicht alles richtig
        </div>
      </div>

      <button @click="showMessage = false">
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
  padding: 14px 18px;
  border-radius: 30px;
  font-size: 22px;
  color: white;
  z-index: 9999;
}

.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.6);
  backdrop-filter: blur(6px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999999;
}

.popup {
  background: #1f1f1f;
  color: white;
  padding: 20px;
  border-radius: 16px;
  width: min(900px,95vw);
  max-height: 90vh;
  overflow: auto;
}

.grid {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.row {
  display: flex;
}

.cell {
  width: 30px;
  height: 30px;
  border: 1px solid #444;
  position: relative;
  background: white;
}

.cell.black {
  background: black;
}

input {
  width: 100%;
  height: 100%;
  border: none;
  text-align: center;
  font-weight: bold;
  transition: all 0.2s ease;
}

input.wrong {
  background: #ff4d4d !important;
  color: white;
}

input.correct {
  background: #4caf50 !important;
  color: white;
}

input.worddone {
  animation: pop 0.4s ease;
  background: #ffd166 !important;
}

.meta {
  position: absolute;
  top: 1px;
  left: 2px;
  font-size: 9px;
  display: flex;
  gap: 2px;
  color: black;
}

.end {
  margin-top: 20px;
  font-size: 22px;
  color: #ff4fa3;
  text-align: center;
}

.fail {
  margin-top: 20px;
  color: yellow;
}

@keyframes pop {
  0% { transform: scale(1); }
  50% { transform: scale(1.25); }
  100% { transform: scale(1); }
}

@keyframes fall {
  to {
    transform: translateY(110vh) rotate(720deg);
    opacity: 0;
  }
}
</style>
