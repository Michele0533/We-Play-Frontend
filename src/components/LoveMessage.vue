<script setup>
import { ref, computed } from 'vue'

const answers = ref({
  genshin: '',
  wife: '',
  husband: '',
  zelda: '',
  link: '',
  silas: '',
  dokomi: '',
  anketten: ''
})

const solved = computed(() => {
  return (
    answers.value.genshin.toLowerCase() === 'genshin' &&
    answers.value.wife.toLowerCase() === 'wife' &&
    answers.value.husband.toLowerCase() === 'husband' &&
    answers.value.zelda.toLowerCase() === 'zelda' &&
    answers.value.link.toLowerCase() === 'link' &&
    answers.value.silas.toLowerCase() === 'silas' &&
    answers.value.dokomi.toLowerCase() === 'dokomi' &&
    answers.value.anketten.toLowerCase() === 'anketten'
  )
})
</script>

<template>
  <div class="crossword">
    <h2>❤️ Unser kleines Kreuzworträtsel ❤️</h2>

    <div class="question">
      <p>1. Unser Lieblingsspiel 🎮</p>
      <input v-model="answers.genshin">
    </div>

    <div class="question">
      <p>2. Englisches Wort für Ehefrau</p>
      <input v-model="answers.wife">
    </div>

    <div class="question">
      <p>3. Englisches Wort für Ehemann</p>
      <input v-model="answers.husband">
    </div>

    <div class="question">
      <p>4. Spielreihe mit Prinzessin Zelda</p>
      <input v-model="answers.zelda">
    </div>

    <div class="question">
      <p>5. Held aus Hyrule</p>
      <input v-model="answers.link">
    </div>

    <div class="question">
      <p>6. Name des kleinen Rackers 🐶</p>
      <input v-model="answers.silas">
    </div>

    <div class="question">
      <p>7. Anime-Convention in Düsseldorf</p>
      <input v-model="answers.dokomi">
    </div>

    <div class="question">
      <p>8. Unser lustiger Insider 😆</p>
      <input v-model="answers.anketten">
    </div>

    <div v-if="solved" class="result">
      💖 Herzlichen Glückwunsch! 💖
      <h1>MY WIFE ❤️</h1>
    </div>
  </div>
</template>

<style scoped>
.crossword {
  background: white;
  padding: 30px;
  border-radius: 20px;
  max-width: 700px;
}

.question {
  margin-bottom: 20px;
}

input {
  width: 250px;
  padding: 10px;
  border-radius: 10px;
  border: 2px solid #ff8db3;
}

.result {
  margin-top: 30px;
  text-align: center;
  color: crimson;
}
</style>
