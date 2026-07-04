<template>
  <div class="genshin-dashboard">

    <button @click="loadData" class="btn">
      🎮 Load Genshin Dashboard
    </button>

    <p v-if="loading">Loading...</p>

    <div v-if="data">

      <!-- ================= BANNERS ================= -->
      <h2>🎉 Current Banners</h2>
      <pre class="box">{{ data.banners }}</pre>

      <!-- ================= MY CHARACTER ================= -->
      <h2>🏆 My Best Character</h2>

      <div v-if="data.me?.bestCharacter" class="card">

        <!-- CHARACTER NAME -->
        <h3>
          {{ data.me.bestCharacter.name }}
          (Lv. {{ data.me.bestCharacter.level }})
        </h3>

        <!-- WEAPON -->
        <div v-if="data.me.bestCharacter.weapon">
          <h4>⚔️ Weapon</h4>
          <pre class="box">{{ data.me.bestCharacter.weapon }}</pre>
        </div>

        <!-- ARTIFACTS GRID -->
        <h4>🛡️ Artifacts</h4>

        <div class="grid">
          <div
            v-for="(art, i) in data.me.bestCharacter.artifacts"
            :key="i"
            class="artifact-card"
          >

            <!-- IMAGE -->
            <img
              :src="`https://enka.network/ui/${art.flat.icon}.png`"
              class="img"
            />

            <!-- MAIN STAT -->
            <p>
              <b>Main:</b>
              {{ art.flat.reliquaryMainstat.mainPropId }}
              ({{ art.flat.reliquaryMainstat.statValue }})
            </p>

            <!-- SUB STATS -->
            <div v-for="(sub, j) in art.flat.reliquarySubstats" :key="j">
              <small>
                {{ sub.appendPropId }}: {{ sub.statValue }}
              </small>
            </div>

          </div>
        </div>
      </div>

      <!-- ================= GIRLFRIEND ================= -->
      <h2>💖 Girlfriend Best Character</h2>

      <div v-if="data.gf?.bestCharacter" class="card">

        <h3>
          {{ data.gf.bestCharacter.name }}
          (Lv. {{ data.gf.bestCharacter.level }})
        </h3>

        <div v-if="data.gf.bestCharacter.weapon">
          <h4>⚔️ Weapon</h4>
          <pre class="box">{{ data.gf.bestCharacter.weapon }}</pre>
        </div>

        <h4>🛡️ Artifacts</h4>

        <div class="grid">
          <div
            v-for="(art, i) in data.gf.bestCharacter.artifacts"
            :key="i"
            class="artifact-card"
          >

            <img
              :src="`https://enka.network/ui/${art.flat.icon}.png`"
              class="img"
            />

            <p>
              <b>Main:</b>
              {{ art.flat.reliquaryMainstat.mainPropId }}
              ({{ art.flat.reliquaryMainstat.statValue }})
            </p>

            <div v-for="(sub, j) in art.flat.reliquarySubstats" :key="j">
              <small>
                {{ sub.appendPropId }}: {{ sub.statValue }}
              </small>
            </div>

          </div>
        </div>

      </div>

    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const API = "https://we-play-backend.onrender.com/api/genshin";

const myUID = "726814599";
const gfUID = "706763823";

const data = ref(null);
const loading = ref(false);

async function loadData() {
  loading.value = true;

  try {
    const [banners, me, gf] = await Promise.all([
      fetch(`${API}/banners/current`).then(r => r.json()),
      fetch(`${API}/player/${myUID}/best`).then(r => r.json()),
      fetch(`${API}/player/${gfUID}/best`).then(r => r.json()),
    ]);

    data.value = { banners, me, gf };

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
  font-family: Arial;
}

.btn {
  padding: 10px 15px;
  background: #6c5ce7;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.btn:hover {
  background: #5a4bd6;
}

.card {
  background: #1a1a1a;
  padding: 15px;
  border-radius: 12px;
  margin-top: 15px;
  color: white;
}

.box {
  background: #111;
  color: #0f0;
  padding: 10px;
  border-radius: 6px;
  overflow-x: auto;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
  margin-top: 10px;
}

.artifact-card {
  background: #111;
  padding: 10px;
  border-radius: 10px;
}

.img {
  width: 70px;
  height: 70px;
  display: block;
  margin-bottom: 5px;
}
</style>
