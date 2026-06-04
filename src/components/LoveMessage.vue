<script setup>
import { ref } from 'vue'

const showMessage = ref(false)
</script>

<template>
  <!-- Button -->
  <button class="love-button" @click="showMessage = true">
    ❤️ Nachricht
  </button>

  <!-- Popup -->
  <div v-if="showMessage" class="popup-overlay">
    <div class="popup">
      <h2>💌 Für dich</h2>

      <p>
        <strong>Tag 1 – Wie alles begann ❤️</strong>
        <br><br>

        Nichts war unerwarteter als deine Liebe zu <strong>m</strong>ir. Ehrlicherweise muss man sagen,
        dass es am Anfang nicht gerade danach aussah. Und dann entstand aus dem Nichts,
        von beiden Seiten aus, diese Liebe. Nichts hätte mich glücklicher machen können.
        <br><br>

        Wh<strong>y</strong> hätte ich auch etwas anderes wollen können als dich? ❤️
        <br><br>

        Aus dem Spaß, dass du mich ankettest und zum Hund machst … einer von uns wird
        angekettet und der andere ist der Hund. xD Wer hätte gedacht, dass daraus einmal
        etwas so <strong>w</strong>underschönes entstehen würde?
        <br><br>

        Als ich gemerkt habe, was für ein toller und <strong>i</strong>mmer besonderer Mensch du bist,
        wollte ich nur noch dich.
        <br><br>

        Auch als du mir den kleinen Racker Silas vorgestellt hast, war alles wie in
        einem Traum. 🥰 Vieles davon werde ich mir e<strong>r</strong>finnern und nie vergessen.
        <br><br>

        Jetzt, wo ich diesen Text schreibe, hast du eine schwere Zeit. Aber lass mich
        deine Hand nehmen und dir zeigen, dass du dich bei mir ausruhen kannst,
        my love. Lass deine Vergangenheit stehen, diese ist mir egal. ❤️
        <br><br>

        Ich bin dankbar für das Schicksal, das uns an diesem Tag zusammengebracht hat.
        Ich hätte niemals gedacht, dass Sayu-Streams einmal so wichtig für mein Leben w<strong>e</strong>rden würden.
        <br><br>

        Und egal, was kommt – ich bin froh, dass wir uns gefunden haben.
        Ich liebe dich. ❤️

        Findest du die versteckte nachricht ^^ 
      </p>

      <button class="close-button" @click="showMessage = false">
        Schließen
      </button>
    </div>
  </div>
</template>

<style scoped>
.love-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 12px 18px;
  border: none;
  border-radius: 30px;
  background: #ff5c8a;
  color: white;
  cursor: pointer;
  font-size: 32px;
  z-index: 1000;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.5);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 1001;
}

.popup {
  background: gray;
  padding: 30px;
  border-radius: 20px;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 0 20px rgba(0,0,0,0.3);
}

.close-button {
  margin-top: 20px;
  padding: 10px 20px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}
</style>
