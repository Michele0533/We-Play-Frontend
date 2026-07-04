<template>
  <div class="genshin-dashboard">
    <button @click="loadData" class="btn">
      🎮 Load Genshin Dashboard
    </button>

    <p v-if="loading">Loading...</p>

    <div v-if="data">
      <h2>🎉 Current Banners</h2>
      <pre>{{ data.banners }}</pre>

      <h2>🏆 My Ranking</h2>
      <pre>{{ data.me }}</pre>

      <h2>💖 Girlfriend Ranking</h2>
      <pre>{{ data.gf }}</pre>

      <h2>📖 Build (Furina)</h2>
      <pre>{{ data.build }}</pre>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const API = "https://we-play-backend.onrender.com/api/genshin";

// 🔁 change these
const myUID = "726814599";
const gfUID = "706763823";

const data = ref(null);
const loading = ref(false);

async function loadData() {
  loading.value = true;

  try {
    const [banners, me, gf, build] = await Promise.all([
      fetch(`${API}/banners/current`).then(r => r.json()),
      fetch(`${API}/player/${myUID}/rankings`).then(r => r.json()),
      fetch(`${API}/player/${gfUID}/rankings`).then(r => r.json()),
      fetch(`${API}/builds/furina`).then(r => r.json())
    ]);

    data.value = {
      banners,
      me,
      gf,
      build
    };
  } catch (err) {
    console.error(err);
    data.value = null;
  }

  loading.value = false;
}
</script>

<style scoped>
.genshin-dashboard {
  padding: 20px;
}

.btn {
  padding: 10px 15px;
  background: #6c5ce7;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 8px;
}

.btn:hover {
  background: #5a4bd6;
}

pre {
  background: #111;
  color: #0f0;
  padding: 10px;
  border-radius: 8px;
  overflow-x: auto;
}
</style>
