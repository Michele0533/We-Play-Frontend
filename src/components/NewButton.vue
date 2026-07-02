import img from "@/assets/u90ycvm2roqe1.png";


<template>
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

      <img class="pixel" src="img" alt="Pixel Cat" />

      <p>
        One lifetime of marriage,
        a <span class="highlight">lifetime</span>
        to go.
        <br /><br />
        Happy Anniversary, my love ❤️
      </p>

      <button class="love" @click="sendLove">
        Love You 💕
      </button>
    </div>

    <!-- floating hearts -->
    <div
      v-for="heart in hearts"
      :key="heart.id"
      class="bg-heart"
      :style="heart.style"
    >
      ❤
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

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
    hearts.value = hearts.value.filter((h) => h.id !== id);
  }, 10000);
}

function sendLove() {
  for (let i = 0; i < 20; i++) {
    setTimeout(createHeart, i * 80);
  }
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

body {
  font-family: "Press Start 2P", cursive;
}

/* background */
.love-window {
  width: 430px;
  border: 6px solid #d97a92;
  border-radius: 18px;
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15);
  animation: pop 0.6s ease;
}

/* floating hearts */
.bg-heart {
  position: fixed;
  bottom: -50px;
  color: #ff5f9b;
  animation: float linear infinite;
  pointer-events: none;
  opacity: 0.8;
}

/* header */
.window-header {
  background: #f8b8ca;
  padding: 15px 18px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 5px solid #d97a92;
}

/* nav */
.window-nav {
  background: #ffe9f0;
  padding: 15px;
  text-align: center;
  color: #ef4778;
  font-size: 18px;
  border-bottom: 5px solid #d97a92;
}

/* body */
.window-body {
  padding: 35px;
  text-align: center;
  background: linear-gradient(45deg, #f7bfd0 25%, transparent 25%),
    linear-gradient(-45deg, #f7bfd0 25%, transparent 25%),
    linear-gradient(45deg, transparent 75%, #f7bfd0 75%),
    linear-gradient(-45deg, transparent 75%, #f7bfd0 75%);
  background-size: 40px 40px;
}

.pixel {
  width: 170px;
  image-rendering: pixelated;
  transition: 0.4s;
}

.pixel:hover {
  transform: scale(1.1) rotate(-3deg);
}

.highlight {
  background: #3a7cff;
  color: white;
  padding: 3px 5px;
}

button.love {
  margin-top: 25px;
  padding: 12px 18px;
  border: none;
  border-radius: 12px;
  background: #ff5b96;
  color: white;
  font-family: inherit;
  cursor: pointer;
  transition: 0.3s;
}

button.love:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 10px 20px rgba(255, 80, 150, 0.3);
}

/* animations */
@keyframes float {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  100% {
    transform: translateY(-120vh) rotate(360deg);
    opacity: 0;
  }
}

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
