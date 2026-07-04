<template>
  <div class="page">

    <button @click="loadData" class="btn">
      🎮 Load Genshin Dashboard
    </button>

    <p v-if="loading">Loading...</p>

    <div v-if="data">

      <!-- ================= BANNERS ================= -->
      <section class="section">
        <h2>🎉 Current Banners</h2>
        <pre class="debug">{{ data.banners }}</pre>
      </section>

      <!-- ================= YOU ================= -->
      <section class="section">

        <h2>🏆 My Best Character</h2>

        <div v-if="data.me?.bestCharacter" class="character-card">

          <!-- PORTRAIT -->
          <img
            class="portrait"
            :src="getCharacterIcon(data.me.bestCharacter)"
          />

          <div class="info">
            <h3>
              {{ data.me.bestCharacter.name }}
            </h3>

            <p>Lv. {{ data.me.bestCharacter.level }}</p>

            <p v-if="data.me.bestCharacter.weapon">
              ⚔️ Weapon equipped
            </p>
          </div>

        </div>

        <!-- ARTIFACTS -->
        <div class="grid">
          <div
            v-for="(art, i) in data.me.bestCharacter.artifacts"
            :key="i"
            class="artifact-card"
          >

            <img
              class="artifact-img"
              :src="`https://enka.network/ui/${art.flat.icon}.png`"
            />

            <div class="artifact-info">
              <b>{{ art.flat.equipType }}</b>

              <p>
                Main:
                {{ art.flat.reliquaryMainstat.mainPropId }}
                ({{ art.flat.reliquaryMainstat.statValue }})
              </p>

              <small
                v-for="(sub, j) in art.flat.reliquarySubstats"
                :key="j"
              >
                {{ sub.appendPropId }}: {{ sub.statValue }} <br />
              </small>

            </div>

          </div>
        </div>

      </section>

      <!-- ================= GF ================= -->
      <section class="section">

        <h2>💖 Girlfriend Best Character</h2>

        <div v-if="data.gf?.bestCharacter" class="character-card">

          <img
            class="portrait"
            :src="getCharacterIcon(data.gf.bestCharacter)"
          />

          <div class="info">
            <h3>{{ data.gf.bestCharacter.name }}</h3>
            <p>Lv. {{ data.gf.bestCharacter.level }}</p>
          </div>

        </div>

      </section>

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
  }

  loading.value = false;
}

/* =========================
 🖼️ CHARACTER ICON HELPER
========================= */
function getCharacterIcon(char) {
  // Enka usually provides icon in different fields depending on version
  const icon =
    char?.icon ||
    char?.avatarIcon ||
    char?.sideIcon ||
    char?.name;

  return `https://enka.network/ui/${icon}.png`;
}
</script>

<style scoped>
.page {
  padding: 20px;
  font-family: Arial;
  background: #0b0b10;
  color: white;
}

.btn {
  padding: 10px 15px;
  background: #6c5ce7;
  border: none;
  color: white;
  border-radius: 10px;
  cursor: pointer;
}

.section {
  margin-top: 20px;
}

/* ================= CHARACTER CARD ================= */
.character-card {
  display: flex;
  align-items: center;
  gap: 15px;
  background: linear-gradient(145deg, #1a1a25, #111);
  padding: 15px;
  border-radius: 15px;
  margin-top: 10px;
}

.portrait {
  width: 90px;
  height: 90px;
  border-radius: 12px;
  border: 2px solid #6c5ce7;
  object-fit: cover;
}

.info h3 {
  margin: 0;
  color: #fff;
}

/* ================= ARTIFACT GRID ================= */
.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
  margin-top: 15px;
}

.artifact-card {
  display: flex;
  gap: 10px;
  background: #141420;
  padding: 10px;
  border-radius: 12px;
}

.artifact-img {
  width: 60px;
  height: 60px;
}

.artifact-info {
  font-size: 12px;
  color: #ccc;
}

/* debug banners */
.debug {
  background: #111;
  padding: 10px;
  border-radius: 10px;
  color: #0f0;
  overflow-x: auto;
}
</style>
