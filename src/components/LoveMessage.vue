<script setup>
import { ref, reactive, computed, watch } from 'vue'

const show = ref(false)
const reveal = ref(false)

// body scroll lock (WICHTIG!)
watch(show, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
})

// Lösung
const solution = [
  ['C','O','M','P','U','T','E','R','S','P','I','E','L','E'],
  ['S','U','P','E','R','M','A','R','K','T',null,null,null,null],
  ['B','E','T','T','S','P','O','R','T',null,null,null,null,null],
  ['A','N','I','M','E',null,null,null,null,null,null,null,null],
  ['L','I','E','B','E',null,null,null,null,null,null,null,null],
  ['F','A','M','I','L','I','E',null,null,null,null,null,null,null],
  ['O','B','S','E','S','S','E','D',null,null,null,null,null,null],
  ['H','Ö','H','E','P','U','N','K','T',null,null,null,null,null],
  ['G','E','N','S','H','I','N',null,null,null,null,null,null,null],
  ['Z','E','L','D','A',null,null,null,null,null,null,null,null,null],
]

const user = reactive(
  Array.from({ length: solution.length }, () =>
    Array.from({ length: solution[0].length }, () => '')
  )
)

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
</script>

<template>

<button class="love-button" @click="show = true">
  ❤️ Nachricht
</button>

<!-- MODAL (kein Scroll-Leak mehr) -->
<div v-if="show" class="overlay">

  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <!-- 🧠 HINTS FIXED & BEAUTIFUL -->
    <div class="hints">
      <h3>Hinweise</h3>
      <ul>
        <li>Computerspiele 🎮</li>
        <li>Supermarkt 🛒</li>
        <li>Bettsport 🛏️</li>
        <li>Anime 🍜</li>
        <li>Liebe ❤️</li>
        <li>Familie 👨‍👩‍👧</li>
        <li>Obsessed 😏</li>
        <li>Höhepunkt 📈</li>
        <li>Spielzeug 🧸</li>
        <li>Herzschlag 💓</li>
        <li>Genshin 🎮</li>
        <li>Zelda 🗡️</li>
      </ul>
    </div>

    <!-- GRID CLEAN -->
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
          />
        </div>
      </div>
    </div>

    <!-- BUTTON -->
    <button class="check" @click="reveal = true">
      Prüfen ❤️
    </button>

    <!-- REVEAL -->
    <div v-if="reveal" class="result">

      <div v-if="solved" class="win">
        💖 Perfekt!
        <h1>MY WIFE ❤️</h1>
        <div class="hearts">💞💞💞</div>
      </div>

      <div v-else class="fail">
        😏 Noch nicht alles richtig
      </div>

    </div>

    <button class="close" @click="show = false">
      Schließen
    </button>

  </div>
</div>

</template>

<style scoped>

/* BUTTON */
.love-button{
  position: fixed;
  bottom: 20px;
  right: 20px;
  background:#ff5c8a;
  border:none;
  border-radius:30px;
  padding:14px;
  font-size:28px;
  color:white;
  z-index:9999;
}

/* MODAL FIX (kein Scroll der App!) */
.overlay{
  position: fixed;
  inset:0;
  background: rgba(0,0,0,0.7);
  display:flex;
  justify-content:center;
  align-items:center;
  z-index:9998;
}

/* POPUP CLEAN */
.popup{
  background:#2b2b2b;
  color:white;
  padding:20px;
  border-radius:16px;
  width:min(900px,95vw);
  max-height:90vh;
  overflow:auto;
  text-align:center;
}

/* HINTS CLEAN */
.hints{
  text-align:left;
  background:#1f1f1f;
  padding:10px;
  border-radius:10px;
  margin-bottom:15px;
}

.hints ul{
  margin:0;
  padding-left:18px;
}

/* GRID FIX */
.grid{
  display:flex;
  flex-direction:column;
  align-items:center;
}

.row{
  display:flex;
}

.cell{
  width:28px;
  height:28px;
  border:1px solid #555;
  display:flex;
  justify-content:center;
  align-items:center;
  background:white;
}

.cell.black{
  background:black;
  border:1px solid black;
}

input{
  width:100%;
  height:100%;
  border:none;
  text-align:center;
  font-weight:bold;
  outline:none;
}

/* RESULT */
.result{
  margin-top:10px;
}

.win{
  color:#ff4fa3;
}

.hearts{
  font-size:24px;
  animation: float 1s infinite alternate;
}

.fail{
  color:yellow;
}

@keyframes float{
  0%{transform:translateY(0)}
  100%{transform:translateY(-6px)}
}
</style>
