<script setup>
import { ref, watch } from 'vue'

/* =========================
   POPUP STATE
========================= */
const showMessage = ref(false)
const redeemed = ref(false)

/* FREEZE BACKGROUND */
watch(showMessage, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
  document.documentElement.style.overflow = val ? 'hidden' : ''
})

function redeem() {
  redeemed.value = true
}
</script>

<template>
  <!-- BUTTON -->
  <button class="love-button" @click="showMessage = true">
    💳 Gutschein öffnen
  </button>

  <!-- OVERLAY -->
  <div v-if="showMessage" class="overlay">
    <div class="card">

      <!-- HEADER -->
      <div class="card-header">
        <div class="title">GUTSCHEIN</div>
        <div class="subtitle">nur für dich ❤️</div>
      </div>

      <!-- BODY -->
      <div class="card-body">
        <div class="big">
          {{ redeemed ? "✔ EINGELÖST 💖" : "1x Wunsch alles was du willst" }}
        </div>

        <div class="small">
          gültig: für immer • personalisiert
        </div>
      </div>

      <!-- FOOTER -->
      <div class="card-footer">

        <button
          v-if="!redeemed"
          class="redeem"
          @click="redeem"
        >
          Einlösen 🎁
        </button>

        <div v-else class="used">
          Schon eingelöst 💖
        </div>

        <button class="close" @click="showMessage = false">
          Schließen
        </button>

      </div>

    </div>
  </div>
</template>

<style scoped>

/* BUTTON */
.love-button{
  position:fixed;
  bottom:20px;
  right:20px;
  background:#ff4fa3;
  border:none;
  padding:14px 18px;
  border-radius:30px;
  color:white;
  font-weight:bold;
  cursor:pointer;
  z-index: 9999;
}

/* OVERLAY (🔥 FIXED FULLSCREEN) */
.overlay{
  position: fixed;
  top: 0;
  left: 0;

  width: 100vw;
  height: 100vh;
  height: 100dvh; /* 🔥 FIX für mobile Browser-Leiste */

  background: rgba(0,0,0,0.65);

  display:flex;
  justify-content:center;
  align-items:center;

  z-index: 99999;
}

/* CARD */
.card{
  width:340px;
  border-radius:22px;
  background:linear-gradient(135deg,#ff4fa3,#ff8cc6);
  color:white;
  padding:22px;
  box-shadow:0 12px 35px rgba(0,0,0,0.45);
  font-family: Arial, sans-serif;
}

/* HEADER */
.card-header{
  text-align:center;
  letter-spacing:2px;
}

.title{
  font-size:22px;
  font-weight:bold;
}

.subtitle{
  font-size:14px;
  opacity:0.9;
  margin-top:4px;
}

/* BODY */
.card-body{
  margin-top:22px;
  text-align:center;
}

.big{
  font-size:18px;
  font-weight:bold;
}

.small{
  font-size:12px;
  opacity:0.8;
  margin-top:6px;
}

/* FOOTER */
.card-footer{
  margin-top:22px;
  display:flex;
  flex-direction:column;
  gap:10px;
}

/* BUTTONS */
.redeem{
  background:white;
  color:#ff4fa3;
  border:none;
  padding:10px;
  border-radius:12px;
  font-weight:bold;
  cursor:pointer;
}

.close{
  background:rgba(255,255,255,0.25);
  border:none;
  padding:8px;
  border-radius:10px;
  color:white;
  cursor:pointer;
}

.used{
  text-align:center;
  font-weight:bold;
}

/* 🔥 GLOBAL FULLSCREEN FIX */
:global(html),
:global(body){
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  overscroll-behavior: none;
}
</style>
