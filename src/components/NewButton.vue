<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from "vue";
import img from "../assets/u90ycvm2roqe1.png";

/* =========================
   MODAL STATE
========================= */
const showMessage = ref(false);

/* lock scroll like coupon style */
watch(showMessage, (val) => {
  document.body.style.overflow = val ? "hidden" : "";
});

/* =========================
   HEART RAIN SYSTEM
========================= */
const hearts = ref([]);
let intervalId;

function createHeart() {
  const id = Date.now() + Math.random();

  hearts.value.push({
    id,
    style: {
      left: Math.random() * 100 + "vw",
      fontSize: 14 + Math.random() * 30 + "px",
      animationDuration: 4 + Math.random() * 4 + "s",
    },
  });

  setTimeout(() => {
    hearts.value = hearts.value.filter((h) => h.id !== id);
  }, 9000);
}

onMounted(() => {
  intervalId = setInterval(createHeart, 200);
});

onBeforeUnmount(() => {
  clearInterval(intervalId);
});
</script>

<template>
  <!-- 💌 BUTTON -->
  <button class="love-button" @click="showMessage = true">
    💌 Open Love
  </button>

  <!-- 🪟 STATIC WINDOW (NO BLUR, NO MOVEMENT) -->
  <div class="love-window">

    <div class="window-header">
      <span>LOVE</span>
      <div class="window-buttons">
        <button>—</button>
        <button>□</button>
        <button>×</button>
      </div>
    </div>

    <div class="window-nav">
      ❤️ • ❤️ • ❤️
    </div>

    <div class="window-body">
      <h2>Yayyy! I love you ♡</h2>

      <!-- 💖 HOVER EFFECT -->
      <img class="pixel" :src="img" />

      <p>
        One lifetime of marriage,
        a <span class="highlight">lifetime</span>
        to go ❤️
      </p>
    </div>
  </div>

  <!-- 🌫️ MODAL (coupon style) -->
  <div v-if="showMessage" class="overlay" @click.self="showMessage = false">
    <div class="card">
      <h2>💖 I love you 💖</h2>

      <img class="popup-img" :src="img" />

      <p>You make everything better ❤️</p>

      <button class="close" @click="showMessage = false">
        Close
      </button>
    </div>
  </div>

  <!-- ❤️ HEART RAIN -->
  <div
    v-for="heart in hearts"
    :key="heart.id"
    class="bg-heart"
    :style="heart.style"
  >
    ❤
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

/* 🪟 STATIC WINDOW */
.love-window {
  width: 430px;
  margin: 60px auto;
  border: 6px solid #d97a92;
  border-radius: 18px;
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0,0,0,0.15);
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

/* 💖 IMAGE HOVER (IMPORTANT PART) */
.pixel {
  width: 170px;
  image-rendering: pixelated;
  transition: 0.35s ease;
  cursor: pointer;
}

.pixel:hover {
  transform: scale(1.15) rotate(-3deg);
  filter: drop-shadow(0 10px 15px rgba(255, 80, 150, 0.4));
}

/* TEXT */
.highlight {
  background: #3a7cff;
  color: white;
  padding: 3px 5px;
}

/* 🌫️ MODAL */
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

/* CARD */
.card {
  width: 340px;
  border-radius: 22px;
  background: linear-gradient(135deg,#ff4fa3,#ff8cc6);
  color: white;
  padding: 22px;
  text-align: center;
}

/* POPUP IMAGE */
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
  opacity: 0.8;
}

/* FLOAT ANIMATION */
@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  10% { opacity: 1; }
  100% { transform: translateY(-120vh); opacity: 0; }
}
</style>
