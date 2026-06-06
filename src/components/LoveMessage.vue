<template>
  <div class="page">

    <div class="voucher" :class="{ used }">

      <div class="header">🎁 GUTSCHEIN</div>

      <div class="body">
        <h2 class="title">{{ title }}</h2>

        <p class="text">{{ text }}</p>

        <div class="value">{{ value }}</div>
      </div>

      <!-- EINLÖSEN BUTTON -->
      <button
        class="button"
        :disabled="used"
        @click="redeem"
      >
        {{ used ? "Schon eingelöst ✔" : "Einlösen ❤️" }}
      </button>

      <!-- RESET BUTTON -->
      <button
        v-if="used"
        class="reset"
        @click="reset"
      >
        Zurücksetzen 🔄
      </button>

    </div>

    <!-- kleine Popup-Animation -->
    <div v-if="showPopup" class="popup">
      🎉 Gutschein eingelöst!
    </div>

  </div>
</template>

<script setup>
import { ref } from "vue"

defineProps({
  title: { type: String, default: "Für dich ❤️" },
  text: { type: String, default: "Dieser Gutschein ist nur für dich." },
  value: { type: String, default: "💝 unbezahlbar" }
})

const used = ref(false)
const showPopup = ref(false)

/* ========================= EINLÖSEN ========================= */
function redeem() {
  if (used.value) return

  used.value = true

  // Popup kurz anzeigen
  showPopup.value = true
  setTimeout(() => {
    showPopup.value = false
  }, 2000)
}

/* ========================= RESET ========================= */
function reset() {
  used.value = false
}
</script>

<style scoped>
.page {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #111;
  font-family: Arial, sans-serif;
}

/* ========================= CARD ========================= */
.voucher {
  width: 340px;
  padding: 25px;
  border-radius: 18px;
  text-align: center;
  color: white;

  background: linear-gradient(135deg, #ff4d7d, #ffb6c1);
  box-shadow: 0 10px 30px rgba(0,0,0,0.4);
  transition: 0.3s;
}

.voucher.used {
  opacity: 0.6;
  filter: grayscale(0.3);
  transform: scale(0.98);
}

/* ========================= TEXT ========================= */
.header {
  font-weight: bold;
  letter-spacing: 2px;
  margin-bottom: 10px;
}

.title {
  font-size: 22px;
  margin: 10px 0;
}

.text {
  font-size: 14px;
  margin-bottom: 15px;
}

.value {
  font-size: 26px;
  font-weight: bold;
  margin: 15px 0;
}

/* ========================= BUTTONS ========================= */
.button {
  padding: 10px 15px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-weight: bold;
  background: white;
  color: #ff4d7d;
  transition: 0.2s;
}

.button:hover {
  transform: scale(1.05);
}

.button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

.reset {
  margin-top: 10px;
  background: transparent;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 12px;
  text-decoration: underline;
}

/* ========================= POPUP ========================= */
.popup {
  position: fixed;
  top: 20px;
  padding: 12px 20px;
  background: #00c853;
  color: white;
  border-radius: 10px;
  font-weight: bold;
  animation: slideDown 0.4s ease;
}

@keyframes slideDown {
  from {
    transform: translateY(-20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
