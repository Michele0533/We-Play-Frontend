<script setup>
import { ref, watch } from "vue";
import img from "../assets/u90ycvm2roqe1.png";

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

/* 🪟 MAIN WINDOW */
.love-window {
  width: 430px;
  margin: 60px auto;
  border: 6px solid #d97a92;
  border-radius: 18px;
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0,0,0,0.15);
  transition: 0.3s ease;
}

/* BLUR WHEN OPEN */
.blur {
  filter: blur(6px);
  transform: scale(0.98);
}

/* HEADER */
.window-header {
  background: #f8b8ca;
  padding: 15px;
  display: flex;
  justify-content: space-between;
}

/* NAV */
.window-nav {
  background: #ffe9f0;
  padding: 15px;
  text-align: center;
}

/* BODY */
.window-body {
  padding: 35px;
  text-align: center;
  background:
    linear-gradient(45deg, #f7bfd0 25%, transparent 25%),
    linear-gradient(-45deg, #f7bfd0 25%, transparent 25%),
    linear-gradient(45deg, transparent 75%, #f7bfd0 75%),
    linear-gradient(-45deg, transparent 75%, #f7bfd0 75%);
  background-size: 40px 40px;
}

.pixel {
  width: 170px;
  image-rendering: pixelated;
}

.highlight {
  background: #3a7cff;
  color: white;
  padding: 3px 5px;
}

/* 🌫️ OVERLAY (coupon style) */
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.65);
  backdrop-filter: blur(6px);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 99999;
}

/* 💖 CARD (coupon style but love themed) */
.card {
  width: 340px;
  border-radius: 22px;
  background: linear-gradient(135deg,#ff4fa3,#ff8cc6);
  color: white;
  padding: 22px;
  text-align: center;
  box-shadow: 0 12px 35px rgba(0,0,0,0.45);
}

/* popup image */
.popup-img {
  width: 120px;
  margin: 10px 0;
}

/* CLOSE */
.close {
  margin-top: 12px;
  background: white;
  color: #ff4fa3;
  border: none;
  padding: 10px;
  border-radius: 12px;
  cursor: pointer;
}

/* ❤️ HEARTS */
.bg-heart {
  position: fixed;
  bottom: -50px;
  color: #ff5f9b;
  animation: float linear infinite;
  pointer-events: none;
}

/* FLOAT */
@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  10% { opacity: 1; }
  100% { transform: translateY(-120vh); opacity: 0; }
}
</style>
