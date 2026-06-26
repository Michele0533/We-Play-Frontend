<template>
  <!-- 💖 FLOATING BUTTON -->
  <button class="head" @click="showGame = true">
    ❓
  </button>

  <!-- 🌑 OVERLAY -->
  <div v-if="showGame" class="overlay">
    <div class="card">

      <div class="text">
Hallo Honey oder sollte ich dich meine künftige ehefrau nennen?
         Du bist für mich das was ich je gesucht habe bzw ich habe danach nie gesucht und dennoch bist du hier , ich wusste icht dass ich so jamden brauche wie dich! alles harmoniert , ist entspannt, kein druck.
         das hat mir das wochenende gezeigt, klar gab es momente wo mal ich oder du es etwas ungewohnt gesehen hat.
         Aber alleine das spazieren mit ava, das kuscheln, das shoppen,das lego bauen,unser inneren kinder rauslassen, scheißelabern, deine familie.
         Zusammen essen und serie schauen.
         das alles hat mein herz daran erinnert dass nach der ganzen shit zeit nun ein engel gespawnt ist und mich beschützt.
         und ja ich sage das oft hase und ich sehe das , dein herz öffnet sich langsam aber sicher und das macht mich zum glücklicshten mann der welt.
         Wie deine traumfrau liebt dich zurück das ist nach allem wirklich das beste was passieren konnte.
         und dein chatakter erst mein schatz! fangen wir nicht damit an wie obsesed ich mit dir bin hase. ICH LIEB DEINE CHATKTER,DEINEN KÖRPER UND ALLES WAS DU FÜR MICH TUST!! <3
      </div>

      <button class="close" @click="showGame = false">
        Schließen
      </button>

    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const showGame = ref(false)

watch(showGame, (val) => {
  document.body.style.overflow = val ? 'hidden' : ''
  document.documentElement.style.overflow = val ? 'hidden' : ''
})
</script>

<style scoped>
.head{
  position: fixed;
  bottom: 20px;
  left: 20px;
  width: 60px;
  height: 60px;
  border: none;
  border-radius: 50%;
  background: #ff4fa3;
  color: white;
  font-size: 26px;
  cursor: pointer;
  z-index: 999999;
  box-shadow: 0 10px 25px rgba(0,0,0,0.3);
}

.overlay{
  position: fixed;
  inset: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0,0,0,0.75);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999999;
}

.card{
  width: min(700px, 90%);
  max-height: 80vh;
  overflow-y: auto;
  background: #1f1f1f;
  color: white;
  padding: 25px;
  border-radius: 18px;
}

.text{
  white-space: pre-wrap;
  line-height: 1.8;
}

.close{
  margin-top: 20px;
  width: 100%;
  background: #ff4fa3;
  border: none;
  padding: 10px;
  color: white;
  border-radius: 10px;
  cursor: pointer;
}
</style>
