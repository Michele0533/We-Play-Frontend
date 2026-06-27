<template>
  <!-- 💖 BUTTON -->
  <button class="head" @click="showGame = true">
    ❓
  </button>

  <!-- 🌑 OVERLAY -->
  <div v-if="showGame" class="overlay">
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

      <!-- HIER KOMMT SPÄTER DEIN TEXT REIN -->
      <div v-else class="text">
        <!-- Hier deinen Brief einfügen -->
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
      }
    }, 700);
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
}

.question {
  margin: 20px 0;
  line-height: 1.6;
  font-size: 18px;
}

input {
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 10px;
  margin-top: 10px;
  margin-bottom: 10px;
  box-sizing: border-box;
}

.btn {
  width: 100%;
  padding: 10px;
  background: #ff4fa3;
  border: none;
  color: white;
  border-radius: 10px;
  cursor: pointer;
  font-size: 16px;
}

.feedback {
  margin-top: 15px;
  min-height: 24px;
  font-weight: bold;
  color: #ffd54f;
}

.text {
  white-space: pre-wrap;
  line-height: 2;
}

.close {
  margin-top: 20px;
  width: 100%;
  background: transparent;
  border: 1px solid white;
  color: white;
  padding: 10px;
  border-radius: 10px;
  cursor: pointer;
}
</style>
