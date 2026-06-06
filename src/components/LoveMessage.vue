<script setup>
import { ref, watch } from 'vue'

const showMessage = ref(false)

/* FREEZE BACKGROUND */
watch(showMessage, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
  document.documentElement.style.overflow = val ? 'hidden' : ''
})
</script>

<template>
<button class="love-button" @click="showMessage = true">
  ❤️ Gutschein öffnen
</button>

<div v-if="showMessage" class="overlay">
  <div class="popup">

    <h2>🎁 Dein Gutschein</h2>

    <div class="voucher">
      <h3>💖 Ein Gutschein für dich 💖</h3>

      <p>
        Dieser Gutschein berechtigt dich zu:
      </p>

      <ul>
        <li>💆‍♀️ 1x Verwöhnabend ohne Stress</li>
        <li>🍫 Süßigkeiten deiner Wahl</li>
        <li>🎮 Gemeinsame Gaming-Session</li>
        <li>💋 100% Aufmerksamkeit von mir</li>
        <li>🛏️ Wunschabend nur für dich</li>
      </ul>

      <p class="love">
        Gültig: für immer ❤️
      </p>
    </div>

    <button @click="showMessage = false">Schließen</button>

  </div>
</div>
</template>

<style scoped>
.love-button{
  position:fixed;
  bottom:20px;
  right:20px;
  background:#ff5c8a;
  border:none;
  padding:14px 18px;
  border-radius:30px;
  color:white;
}

.overlay{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,0.6);
  display:flex;
  justify-content:center;
  align-items:center;
}

.popup{
  background:#1f1f1f;
  color:white;
  padding:20px;
  border-radius:16px;
  width:320px;
  text-align:center;
}

.voucher{
  background:linear-gradient(135deg,#ff4fa3,#ff8cc6);
  padding:16px;
  border-radius:12px;
  margin-top:10px;
  color:white;
}

.voucher ul{
  text-align:left;
  padding-left:18px;
}

.love{
  margin-top:10px;
  font-weight:bold;
}

button{
  margin-top:15px;
}
</style>
