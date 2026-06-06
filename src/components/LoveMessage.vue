<template>
  <div class="page">
    <div class="voucher">

      <div class="header">🎁 GUTSCHEIN</div>

      <div class="body">
        <h2 class="title">{{ title }}</h2>

        <p class="text">
          {{ text }}
        </p>

        <div class="value">
          {{ value }}
        </div>
      </div>

      <button class="button" @click="redeem">
        Einlösen ❤️
      </button>

      <div v-if="used" class="used">
        ✔ Gutschein wurde eingelöst
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref } from "vue"

const props = defineProps({
  title: { type: String, default: "Für dich ❤️" },
  text: { type: String, default: "Dieser Gutschein gehört nur dir." },
  value: { type: String, default: "💝 unbezahlbar" }
})

const used = ref(false)

function redeem() {
  used.value = true
}
</script>

<style scoped>
.page {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #111;
}

.voucher {
  width: 320px;
  padding: 25px;
  border-radius: 18px;
  background: linear-gradient(135deg, #ff4d7d, #ffb6c1);
  color: white;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0,0,0,0.4);
  font-family: Arial, sans-serif;
}

.header {
  font-weight: bold;
  font-size: 16px;
  margin-bottom: 10px;
  letter-spacing: 2px;
}

.title {
  font-size: 22px;
  margin: 10px 0;
}

.text {
  font-size: 14px;
  margin-bottom: 15px;
}

.value {
  font-size: 26px;
  font-weight: bold;
  margin: 15px 0;
}

.button {
  margin-top: 10px;
  padding: 10px 15px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-weight: bold;
  background: white;
  color: #ff4d7d;
  transition: 0.2s;
}

.button:hover {
  transform: scale(1.05);
}

.used {
  margin-top: 10px;
  font-size: 13px;
  opacity: 0.9;
}
</style>
