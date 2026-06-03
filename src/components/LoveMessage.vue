<script setup>
import { ref } from 'vue'

const showMessage = ref(false)
</script>

<template>
  <!-- Button -->
  <button class="love-button" @click="showMessage = true">
    ❤️ Nachricht
  </button>

  <!-- Popup -->
  <div v-if="showMessage" class="popup-overlay">
    <div class="popup">
      <h2>💌 Für dich</h2>

      <p>
        Hallo mein Schatz, wie es aussieht, hast du diese Funktion gefunden.
        Als erste Nachricht werde ich dir erst mal Bescheid geben, wie das
        funktioniert.
        <br><br>
        Jeden Tag werde ich nun einen kleinen Überraschungstext schreiben.
        Jeder Tag wird anders sein und jeden Tag werde ich mir etwas Neues
        einfallen lassen. Bedeutet für dich: Schau gerne öfter vorbei. ❤️
        <br><br>
        Ich liebe dich. 🥰
      </p>

      <button class="close-button" @click="showMessage = false">
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
  font-size: 16px;
  z-index: 1000;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 1001;
}

.popup {
  background: gray;
  padding: 30px;
  border-radius: 20px;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 0 20px rgba(0,0,0,0.3);
}

.close-button {
  margin-top: 20px;
  padding: 10px 20px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}
</style>
