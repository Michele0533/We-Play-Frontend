<script setup>
import { ref, onMounted } from "vue";

const API = "http://localhost:3000";

const author = ref("Micky");
const text = ref("");
const entries = ref([]);


async function loadDiary() {

  const res = await fetch(`${API}/api/diary`);

  entries.value = await res.json();

}



async function saveEntry() {

  if (!text.value.trim()) return;


  await fetch(`${API}/api/diary`, {

    method: "POST",

    headers:{
      "Content-Type":"application/json"
    },

    body:JSON.stringify({

      author: author.value,
      text: text.value

    })

  });


  text.value = "";

  loadDiary();

}



async function deleteEntry(id){

  await fetch(`${API}/api/diary/${id}`,{
    method:"DELETE"
  });


  loadDiary();

}



onMounted(loadDiary);

</script>



<template>

<div class="chat-container">


<!-- Header -->

<div class="chat-header">

❤️ Unser Tagebuch ❤️

</div>



<!-- Nachrichten -->

<div class="messages">


<div
v-for="entry in entries"
:key="entry._id"

class="message"

:class="entry.author"

>


<div class="name">

{{entry.author}}

</div>


<div class="bubble">

{{entry.text}}

</div>


<div class="time">

{{new Date(entry.createdAt).toLocaleString("de-DE")}}

</div>



<button 
class="delete"
@click="deleteEntry(entry._id)"
>
🗑
</button>


</div>


</div>





<!-- Schreiben -->

<div class="input-area">


<select v-model="author">

<option>Micky</option>
<option>Tina</option>

</select>



<textarea
v-model="text"
placeholder="Schreibe eine Nachricht..."
></textarea>



<button @click="saveEntry">

💌 Senden

</button>


</div>


</div>


</template>




<style scoped>


.chat-container{

width:90%;

max-width:1000px;

height:75vh;

margin:40px auto;

display:flex;

flex-direction:column;

background:#efe7df;

border-radius:20px;

overflow:hidden;

box-shadow:
0 10px 30px rgba(0,0,0,.25);

}



/* HEADER */

.chat-header{

background:#ff5c99;

color:white;

font-size:25px;

font-weight:bold;

padding:20px;

text-align:center;

}



/* CHAT */

.messages{

flex:1;

padding:25px;

overflow-y:auto;

display:flex;

flex-direction:column;

gap:15px;

}




.message{

max-width:70%;

position:relative;

}



.message.Micky{

align-self:flex-end;

text-align:right;

}



.message.Tina{

align-self:flex-start;

}




.name{

font-size:13px;

font-weight:bold;

margin-bottom:5px;

}



.bubble{

padding:15px;

border-radius:18px;

font-size:17px;

white-space:pre-wrap;

}



.Micky .bubble{

background:#a7ddff;

border-bottom-right-radius:3px;

}



.Tina .bubble{

background:#ffb6d9;

border-bottom-left-radius:3px;

}



.time{

font-size:11px;

color:#777;

margin-top:5px;

}




.delete{

background:none;

border:none;

cursor:pointer;

}




/* INPUT */

.input-area{

background:white;

padding:15px;

display:flex;

gap:10px;

}



select{

border-radius:10px;

padding:10px;

}



textarea{

flex:1;

height:50px;

resize:none;

padding:10px;

border-radius:12px;

border:1px solid #ccc;

}



button{

background:#ff5c99;

color:white;

border:none;

padding:12px 20px;

border-radius:20px;

cursor:pointer;

}


</style>
