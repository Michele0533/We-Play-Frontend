<script setup>
import { ref, reactive, computed, watch } from 'vue'

const show = ref(false)
const reveal = ref(false)

// Scroll fix
watch(show, (v) => {
  document.body.style.overflow = v ? 'hidden' : ''
})

/*
🧩 ECHTES KREUZWORT (sauber gebaut)
nur Wörter die sinnvoll kreuzen:
*/
const solution = [
  ['C','O','M','P','U','T','E','R','S','P','I','E','L','E'],
  [null,null,null,null,null,null,'A',null,null,null,null,null,null,null],
  ['S','U','P','E','R','M','A','R','K','T',null,null,null,null],
  [null,null,null,null,null,null,'I',null,null,null,null,null,null,null],
  ['B','E','T','T','S','P','O','R','T',null,null,null,null,null],
  [null,null,null,null,null,null,'M',null,null,null,null,null,null,null],
  ['A','N','I','M','E',null,'E',null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null],
  ['L','I','E','B','E',null,null,null,null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null,null,null,null,null],
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

<div v-if="show" class="overlay">
  <div class="popup">

    <h2>🧩 Kreuzworträtsel</h2>

    <!-- 🧠 HINTS (JETZT WIRKLICH HINTS, KEINE WÖRTER!) -->
    <div class="hints">
      <h3>Hinweise</h3>
      <ul>
        <li>🎮 Etwas das man am PC spielt</li>
        <li>🛒 Ort zum Einkaufen</li>
        <li>🛏️ „Sport“ im Bett 😏</li>
        <li>🍜 Japanische Animationsform</li>
        <li>❤️ Gefühl zwischen zwei Menschen</li>
        <li>👨‍👩‍👧 Gruppe von Menschen die zusammengehören</li>
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
.love-button{
  position:fixed;
  bottom:20px;
  right:20px;
  background:#ff5c8a;
  border:none;
  border-radius:30px;
  padding:14px;
  font-size:28px;
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
  text-align:center;
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
  width:26px;
  height:26px;
  border:1px solid #444;
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
  text-align:center;
  border:none;
  font-weight:bold;
  outline:none;
}

.check{
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
