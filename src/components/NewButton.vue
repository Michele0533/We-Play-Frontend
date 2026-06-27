<template>
  <!-- 💖 BUTTON -->
  <button class="head" @click="showGame = true">
    ❓
  </button>

  <!-- 🌑 OVERLAY -->
  <div v-if="showGame" class="overlay">

    <!-- 🎉 CONFETTI -->
    <div v-if="showConfetti" class="confetti">
      🎉 💖 🎉 💖 🎉 💖 🎉 💖 🎉 💖 🎉
    </div>

    <div class="card">

      <!-- LEVELS -->
      <div v-if="!finished">
        <h2>Level {{ level + 1 }}/{{ levels.length }}</h2>

        <p class="question">
          {{ levels[level].question }}
        </p>

        <input
          v-model="input"
          @keyup.enter="submit"
          placeholder="Antwort eingeben..."
        />

        <button class="btn" @click="submit">
          Bestätigen
        </button>

        <p class="feedback">{{ feedback }}</p>
      </div>

      <!-- DONE -->
      <div v-else class="text">
        <!-- DEIN TEXT HIER -->
      </div>

      <button class="close" @click="showGame = false">
        Schließen
      </button>

    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const showGame = ref(false);

const level = ref(0);
const input = ref("");
const feedback = ref("");
const finished = ref(false);
const showConfetti = ref(false);

const levels = [
  {
    question:
      "1. Mich findet man auf hoher See und es gibt mehrere von mir, um ins Schloss zu gelangen. Wer bin ich?",
    answer: "triforce",
  },
  {
    question:
      "2. Ich bin ein treuer Begleiter vom Traveler und erkunde Teyvat. Wer bin ich?",
    answer: "paimon",
  },
  {
    question:
      "3. Mich kann man fühlen, aber weder sehen noch anfassen. Was bin ich?",
    answer: "liebe",
  },
  {
    question:
      "4. Ich bin teurer als jeder Lambo. Wer bin ich? (Hint: Genshin Charakter)",
    answer: "lohen",
  },
];

watch(showGame, (val) => {
  document.body.style.overflow = val ? "hidden" : "";
  document.documentElement.style.overflow = val ? "hidden" : "";
});

function submit() {
  const answer = input.value.toLowerCase().trim();

  if (answer === levels[level.value].answer.toLowerCase()) {
    feedback.value = "💖 Richtig!";

    setTimeout(() => {
      feedback.value = "";
      input.value = "";
      level.value++;

      if (level.value >= levels.length) {
        finished.value = true;

        // 🎉 CONFETTI START
        showConfetti.value = true;

        setTimeout(() => {
          showConfetti.value = false;
        }, 4000);
      }
    }, 600);
  } else {
    feedback.value = "❌ Leider falsch. Versuch es nochmal.";
  }
}
</script>

<style scoped>
.head {
  position: fixed;
  bottom: 20px;
  left: 20px;
  width: 60px;
  height: 60px;
  border: none;
  border-radius: 50%;
  background: #ff4fa3;
  color: white;
  font-size: 26px;
  cursor: pointer;
  z-index: 999999;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
}

.overlay {
  position: fixed;
  inset: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.75);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999999;
}

.card {
  width: min(700px, 90%);
  max-height: 80vh;
  overflow-y: auto;
  background: #1f1f1f;
  color: white;
  padding: 25px;
  border-radius: 18px;
  position: relative;
}

.question {
  margin: 20px 0;
  line-height: 1.6;
}

input {
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 10px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.btn {
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 10px;
  background: #ff4fa3;
  color: white;
  cursor: pointer;
}

.feedback {
  margin-top: 10px;
  min-height: 20px;
  font-weight: bold;
}

.text {
  white-space: pre-wrap;
  line-height: 2;
}

.close {
  margin-top: 20px;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 10px;
  background: #444;
  color: white;
  cursor: pointer;
}

/* 🎉 CONFETTI */
.confetti {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9999999;

  display: flex;
  justify-content: space-around;
  align-items: flex-start;

  font-size: 30px;
  animation: fall 4s linear forwards;
}

@keyframes fall {
  0% {
    transform: translateY(-20vh);
    opacity: 1;
  }
  100% {
    transform: translateY(120vh);
    opacity: 0;
  }
}
</style>
