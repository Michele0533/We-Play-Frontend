<template>
  <!-- MAIN BACKGROUND (ONLY ONCE) -->
  <div class="love-window" :class="{ blur: open }">

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

      <img class="pixel" :src="img" alt="Pixel Cat" />

      <p>
        One lifetime of marriage,
        a <span class="highlight">lifetime</span>
        to go.
        <br /><br />
        Happy Anniversary ❤️
      </p>
    </div>

  </div>

  <!-- 💌 BUTTON -->
  <button class="open-btn" @click="open = true">
    💌 Open
  </button>

  <!-- 🌫️ POPUP (ONLY ONE WINDOW) -->
  <div v-if="open" class="overlay" @click.self="open = false">
    <div class="popup">
      <h2>💖 I love you 💖</h2>

      <img class="popup-img" :src="img" />

      <p>You make everything better ❤️</p>

      <button class="close-btn" @click="open = false">
        Close
      </button>
    </div>
  </div>

  <!-- ❤️ hearts -->
  <div
    v-for="heart in hearts"
    :key="heart.id"
    class="bg-heart"
    :style="heart.style"
  >
    ❤
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import img from "../assets/u90ycvm2roqe1.png";

const open = ref(false);
const hearts = ref([]);

let intervalId;

function createHeart() {
  const id = Date.now() + Math.random();

  hearts.value.push({
    id,
    style: {
      left: Math.random() * 100 + "vw",
      fontSize: 15 + Math.random() * 35 + "px",
      animationDuration: 5 + Math.random() * 5 + "s",
    },
  });

  setTimeout(() => {
    hearts.value = hearts.value.filter(h => h.id !== id);
  }, 10000);
}

onMounted(() => {
  intervalId = setInterval(createHeart, 220);
});

onBeforeUnmount(() => {
  clearInterval(intervalId);
});
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* MAIN BACKGROUND WINDOW */
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
  padding: 15px 18px;
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
}

.pixel {
  width: 170px;
}

/* BUTTON */
.open-btn {
  position: fixed;
  bottom: 20px;
  left: 20px;
  background: #ff5b96;
  color: white;
  border: none;
  padding: 10px 14px;
  border-radius: 10px;
  cursor: pointer;
  z-index: 10;
}

/* OVERLAY */
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.35);
  backdrop-filter: blur(6px);
  display: flex;
  justify-content: center;
  align-items: center;
}

/* POPUP */
.popup {
  background: white;
  padding: 30px;
  border-radius: 16px;
  text-align: center;
  width: 300px;
}

/* HEARTS */
.bg-heart {
  position: fixed;
  bottom: -50px;
  animation: float linear infinite;
}

/* animation */
@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  100% { transform: translateY(-120vh); opacity: 0; }
}
</style>
