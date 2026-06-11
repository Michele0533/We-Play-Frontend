<script setup>
import { ref, computed, watch } from 'vue'

/* =========================
   STATE
========================= */
const showGame = ref(false)
const level = ref(0)
const input = ref('')
const tries = ref(0)
const feedback = ref('')
const animateFeedback = ref(false)
const showConfetti = ref(false)
const finished = ref(false)

/* =========================
   BODY LOCK (FULLSCREEN FIX)
========================= */
watch(showGame, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
  document.documentElement.style.overflow = val ? 'hidden' : ''
})

/* =========================
   LEVELS
========================= */
const levels = [
  {
    question: "An welchen Genshin Charakter denke ich?",
    answer: "xianyun",
    tips: ["🗡️ Catalyst", "🌪️ Anemo", "📜 4.4 Patch", "🏔️ Liyue", "☁️ Cloud Retainer"]
  },
  {
    question: "An welches Gebiet denke ich?",
    answer: "sumeru",
    tips: ["🌿 Dendro ", "Wald ", "Wüste", "👑 Weisheit", "👧 Nahida"]
  },
  {
    question: "Emoji Charakter?",
    answer: "linnea",
    tips: ["🪨", "🪨🏹", "🪨🏹💚", "🪨🏹💚💥", "🪨🏹💚💥⛏️"]
  },
  {
    question: "Letzte Frage ❤️",
    answer: "Honey",
    tips: ["💖 wichtig", "💖 immer da", "💖 Lieblingsperson", "💖 Herz", "💖 du"]
  }
]

const current = computed(() => levels[level.value] || null)

/* =========================
   GAME LOGIC
========================= */
function submit() {
  if (!current.value) return

  const ans = input.value.toLowerCase().trim()

  if (ans === current.value.answer) {
    feedback.value = "✅ Richtig!"
    nextLevel()
    return
  }

  tries.value++
  animateFeedback.value = true
  setTimeout(() => animateFeedback.value = false, 300)

  if (tries.value >= 5) {
    feedback.value = `❌ Lösung: ${current.value.answer}`
    nextLevel()
    return
  }

  feedback.value = current.value.tips[tries.value - 1]
}

/* =========================
   NEXT LEVEL
========================= */
function nextLevel() {
  setTimeout(() => {
    level.value++
    input.value = ''
    tries.value = 0
    feedback.value = ''

    if (level.value >= levels.length) {
      finished.value = true
      showConfetti.value = true
      setTimeout(() => showConfetti.value = false, 1500)
    }
  }, 400)
}

/* =========================
   RESET
========================= */
function resetGame() {
  level.value = 0
  tries.value = 0
  finished.value = false
}
</script>

<template>

<!-- 💖 FLOATING BUTTON -->
<button class="head" @click="showGame = true">
  💖
</button>

<!-- 🌑 FULLSCREEN OVERLAY -->
<div v-if="showGame" class="overlay">

  <!-- 🎉 CONFETTI -->
  <div v-if="showConfetti" class="confetti">
    🎉💖🎉💖🎉
  </div>

  <div class="card">

    <h2 v-if="!finished">Level {{ level + 1 }}/4</h2>

    <div v-if="!finished">

      <p class="question">{{ current.question }}</p>

      <input v-model="input" @keyup.enter="submit" />

      <button class="btn" @click="submit">OK</button>

      <p class="feedback" :class="{ shake: animateFeedback }">
        {{ feedback }}
      </p>

      <p class="tries">Versuche: {{ tries }}/5</p>

    </div>

    <div v-else class="done">
      💖 GAME COMPLETE 💖
      <br><br>
      <button @click="resetGame">Neu starten</button>
    </div>

    <button class="close" @click="showGame = false">
      Schließen
    </button>

  </div>
</div>

</template>

<style scoped>

/* 💖 BUTTON */
.head{
  position: fixed;
  bottom: 20px;
  left: 20px;

  width: 60px;
  height: 60px;

  background: #ff4fa3;
  border-radius: 50%;
  border: none;

  display: flex;
  justify-content: center;
  align-items: center;

  font-size: 26px;
  cursor: pointer;

  box-shadow: 0 10px 25px rgba(0,0,0,0.3);

  z-index: 999999;
}

/* 🌑 FULLSCREEN OVERLAY FIX */
.overlay{
  position: fixed;
  inset: 0;
  width: 100vw;
  height: 100vh;

  background: rgba(0,0,0,0.75);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 999999;
}

/* 📱 CARD */
.card{
  width: 340px;
  background: #1f1f1f;
  color: white;
  padding: 20px;
  border-radius: 18px;
  text-align: center;
}

/* INPUT */
input{
  width: 100%;
  padding: 10px;
  border-radius: 10px;
  border: none;
  margin-top: 10px;
}

/* BUTTON */
.btn{
  width: 100%;
  margin-top: 10px;
  background: #ff4fa3;
  border: none;
  padding: 10px;
  color: white;
  border-radius: 10px;
}

/* FEEDBACK */
.feedback{
  min-height: 20px;
  color: #ffcc00;
}

/* TRIES */
.tries{
  font-size: 12px;
  opacity: 0.6;
}

/* CLOSE */
.close{
  margin-top: 10px;
  background: transparent;
  border: 1px solid white;
  color: white;
  padding: 6px 10px;
  border-radius: 8px;
}

/* CONFETTI */
.confetti{
  position: absolute;
  top: 20%;
  font-size: 30px;
}

/* SHAKE */
.shake{
  animation: shake 0.3s;
}

@keyframes shake{
  0%,100%{transform:translateX(0)}
  25%{transform:translateX(-5px)}
  50%{transform:translateX(5px)}
  75%{transform:translateX(-3px)}
}

</style>
