<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from "vue";
import img from "../assets/u90ycvm2roqe1.png";

/* =========================
   STATE (WINDOW OPEN/CLOSE)
========================= */
const open = ref(false);

/* lock scroll */
watch(open, (val) => {
  document.body.style.overflow = val ? "hidden" : "";
});

/* =========================
   HEARTS
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
  <button class="love-button" @click="open = true">
    💌 Open Love
  </button>

  <!-- 🌫️ OVERLAY (WINDOW OPENS HERE) -->
  <div v-if="open" class="overlay" @click.self="open = false">

    <div class="love-window">

      <div class="window-header">
        <span>Einmoatiges!</span>
        <button @click="open = false">×</button>
      </div>

      <div class="window-nav">
        ❤️ • ❤️ • ❤️
      </div>

      <div class="window-body">
        <h2>Yayyy! Io Ti Amo ♡</h2>

        <img class="pixel" :src="img" />

        <p>
          Einen Monat und es wird noch,
          a <span class="highlight">Lebenlang</span>
          Amore io❤️
        </p>
      </div>

    </div>

  </div>

  <!-- ❤️ HEARTS -->
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

/* 🌫️ OVERLAY */
.overlay {
  position: fixed;
  inset: 0;
 backdrop-filter: blur(6px);
background: rgba(0,0,0,0.65);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 99999;
}

/* 🪟 WINDOW (NOW OPENS) */
.love-window {
  width: 430px;
  border: 6px solid #d97a92;
  border-radius: 18px;
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0,0,0,0.15);
  animation: pop 0.25s ease;
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
  transition: 0.3s;
}

.pixel:hover {
  transform: scale(1.15) rotate(-3deg);
}

/* ❤️ HEARTS */
.bg-heart {
  position: fixed;
  bottom: -50px;
  color: #ff5f9b;
  animation: float linear infinite;
  pointer-events: none;
}

/* ANIMATIONS */
@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  10% { opacity: 1; }
  100% { transform: translateY(-120vh); opacity: 0; }
}

@keyframes pop {
  from { transform: scale(0.8); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}
</style>
