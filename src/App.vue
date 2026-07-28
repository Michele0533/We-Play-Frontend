<script setup>
import { ref } from 'vue'

import GameView from './components/GameView.vue'
import MovieView from './components/MovieView.vue'
import LoveMessage from './components/LoveMessage.vue'
import NewButton from './components/NewButton.vue'
import Diary from './components/Diary.vue'


const page = ref('games')

const showPopup = ref(true)

</script>


<template>

  <!-- 🎁 Gutschein Popup -->
  <div v-if="showPopup" class="popup-overlay">

    <div class="popup">

      <h2>🎁 Gutschein</h2>

      <p>
        <strong>
          GUTSCHEIN NOCH NICHT EINGELÖST!
        </strong>
      </p>

      <button @click="showPopup = false">
        Verstanden ❤️
      </button>

    </div>

  </div>



  <!-- Hauptinhalt -->

  <h1>
    WePlay 🎮
  </h1>



  <nav>

    <button @click="page = 'games'">
      🎮 Games
    </button>


    <button @click="page = 'movies'">
      🎬 Movies
    </button>


    <button @click="page = 'genshin'">
      ⚡ Genshin
    </button>


    <button @click="page = 'diary'">
      📖 Tagebuch
    </button>

  </nav>



  <LoveMessage />


  <NewButton />



  <!-- Seiten -->

  <GameView 
    v-if="page === 'games'" 
  />


  <MovieView 
    v-if="page === 'movies'" 
  />


  <GenshinDashboard 
    v-if="page === 'genshin'" 
  />


  <Diary 
    v-if="page === 'diary'" 
  />



</template>



<style scoped>

.popup-overlay {

  position: fixed;
  inset: 0;

  background: rgba(0,0,0,0.65);

  display:flex;
  justify-content:center;
  align-items:center;

  z-index:9999;

}



.popup {

  background:white;

  color:#222;

  padding:35px;

  border-radius:16px;

  text-align:center;

  box-shadow:
  0 10px 30px rgba(0,0,0,.4);

}



.popup h2 {

  color:#d32f2f;

}



.popup button {

  margin-top:20px;

  padding:12px 25px;

  border:none;

  border-radius:10px;

  background:#ff5c99;

  color:white;

  cursor:pointer;

}


</style>
