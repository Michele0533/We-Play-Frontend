<script setup>
import { ref, reactive, computed } from 'vue'

const showMessage = ref(false)

// 10x10 Lösung (deine Wörter eingebaut)
const solution = [
  ['G','E','N','S','H','I','N',null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
  ['W','I','F','E',null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
  ['Z','E','L','D','A',null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
  ['L','I','N','K',null,null,null,null,null,null],
  [null,null,null,null,null,null,null,null,null,null],
  ['S','I','L','A','S',null,null,null,null,null],
  ['D','O','K','O','M','I',null,null,null,null]
]

// User Input Grid
const user = reactive(
  Array.from({ length: 10 }, () =>
    Array.from({ length: 10 }, () => '')
  )
)

// Check if solved
const solved = computed(() => {
  for (let y = 0; y < 10; y++) {
    for (let x = 0; x < 10; x++) {
      if (solution[y][x]) {
        if (user[y][x].toUpperCase() !== solution[y][x]) {
          return false
        }
      }
    }
  }
  return true
})

function close() {
  showMessage.value = false
}
</script>

<template>
  <!-- Button -->
  <button class="love-button" @click="showMessage = true">
    ❤️ Nachricht
  </button>

  <!-- Popup -->
  <div v-if="showMessage" class="popup-overlay">
    <div class="popup">

      <h2>🧩 Kreuzworträtsel</h2>
      <p>Löse alle Wörter und finde die Nachricht ❤️</p>

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

      <!-- SUCCESS -->
      <div v-if="solved" class="success">
        💖 Richtig!
        <h1>MY WIFE ❤️</h1>
      </div>

      <button class="close-button" @click="close">
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
  padding: 12px 18px;
  border: none;
  border-radius: 30px;
  background: #ff5c8a;
  color: white;
  cursor: pointer;
  font-size: 32px;
}

.popup-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup {
  background: gray;
  padding: 25px;
  border-radius: 20px;
  text-align: center;
}

.grid {
  display: inline-block;
  margin: 20px 0;
}

.row {
  display: flex;
}

.cell {
  width: 32px;
  height: 32px;
  border: 1px solid #ccc;
}

input {
  width: 100%;
  height: 100%;
  text-align: center;
  border: none;
  outline: none;
  font-weight: bold;
}

.black {
  width: 100%;
  height: 100%;
  background: black;
}

.success {
  margin-top: 15px;
  color: hotpink;
}

.close-button {
  margin-top: 15px;
  padding: 10px 15px;
}
</style>
