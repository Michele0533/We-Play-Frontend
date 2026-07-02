<script setup>
import { ref, watch } from "vue";
import img from "@/assets/u90ycvm2roqe1.png";

/* =========================
   STATE
========================= */
const showMessage = ref(false);

/* =========================
   FREEZE BACKGROUND (like your example)
========================= */
watch(showMessage, (val) => {
  document.body.style.overflow = val ? "hidden" : "";
  document.documentElement.style.overflow = val ? "hidden" : "";
});
</script>

<template>
  <!-- 💌 BUTTON -->
  <button class="love-button" @click="showMessage = true">
    💌 Open Love
  </button>

  <!-- 🌫️ OVERLAY -->
  <div v-if="showMessage" class="overlay" @click.self="showMessage = false">

    <div class="card">

      <!-- HEADER -->
      <div class="card-header">
        <div class="title">LOVE LETTER</div>
        <div class="subtitle">just for you ❤️</div>
      </div>

      <!-- BODY -->
      <div class="card-body">
        <h2>Yayyy! I love you ♡</h2>

        <img class="pixel" :src="img" alt="cat" />

        <p>
          One lifetime of marriage,
          a <span class="highlight">lifetime</span>
          to go ❤️
        </p>
      </div>

      <!-- FOOTER -->
      <div class="card-footer">
        <button class="close" @click="showMessage = false">
          Close
        </button>
      </div>

    </div>
  </div>
</template>

<style scoped>

/* 💌 BUTTON */
.love-button {
  position: fixed;
  bottom: 20px;
  left: 20px;
  background: #ff4fa3;
  border: none;
  padding: 14px 18px;
  border-radius: 30px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  z-index: 9999;
}

/* 🌫️ OVERLAY */
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.6);
  backdrop-filter: blur(6px);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 99999;
}

/* 💖 CARD */
.card {
  width: 340px;
  border-radius: 22px;
  background: linear-gradient(135deg, #ff4fa3, #ff8cc6);
  color: white;
  padding: 22px;
  box-shadow: 0 12px 35px rgba(0,0,0,0.45);
  text-align: center;
  animation: pop 0.25s ease;
}

/* HEADER */
.title {
  font-size: 20px;
  font-weight: bold;
  letter-spacing: 2px;
}

.subtitle {
  font-size: 12px;
  opacity: 0.9;
}

/* BODY */
.card-body {
  margin-top: 15px;
}

.pixel {
  width: 140px;
  margin: 12px 0;
}

.highlight {
  background: white;
  color: #ff4fa3;
  padding: 2px 5px;
  border-radius: 4px;
}

/* CLOSE BUTTON */
.close {
  margin-top: 15px;
  background: white;
  color: #ff4fa3;
  border: none;
  padding: 10px;
  border-radius: 12px;
  cursor: pointer;
  font-weight: bold;
}

/* ANIMATION */
@keyframes pop {
  from {
    transform: scale(0.8);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
