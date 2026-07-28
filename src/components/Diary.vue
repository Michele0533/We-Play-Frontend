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

    headers: {
      "Content-Type": "application/json"
    },

    body: JSON.stringify({
      author: author.value,
      text: text.value
    })

  });


  text.value = "";

  loadDiary();
}



async function deleteEntry(id) {

  await fetch(`${API}/api/diary/${id}`, {
    method: "DELETE"
  });

  loadDiary();

}



onMounted(loadDiary);

</script>


<template>

<div class="diary">

<h2>📖 Unser Tagebuch ❤️</h2>


<select v-model="author">

<option>Micky</option>
<option>Tina</option>

</select>


<textarea
v-model="text"
placeholder="Schreibe deine Gedanken..."
></textarea>


<button @click="saveEntry">
💌 Speichern
</button>



<div
v-for="entry in entries"
:key="entry._id"
class="entry"
:class="entry.author"
>


<div class="header">

<strong>
{{ entry.author === "Micky" ? "💙 Micky" : "💗 Tina" }}
</strong>


<small>
{{ new Date(entry.createdAt).toLocaleString("de-DE") }}
</small>

</div>


<p>
{{ entry.text }}
</p>


<button
class="delete"
@click="deleteEntry(entry._id)"
>
🗑️
</button>


</div>


</div>

</template>



<style scoped>

.diary {

max-width:700px;
margin:40px auto;
padding:25px;

background:#fff0f5;
border-radius:20px;

color:#333;

}


h2 {

text-align:center;
color:#ff4f9a;

}



select,
textarea {

width:100%;
padding:12px;

margin-top:15px;

border-radius:12px;
border:1px solid #ddd;

}



textarea {

height:130px;
resize:none;

}



button {

margin-top:15px;

padding:12px 25px;

border:none;

border-radius:20px;

background:#ff5c99;

color:white;

cursor:pointer;

}



.entry {

margin-top:25px;

padding:20px;

background:white;

border-radius:15px;

}



.entry.Micky {

border-left:7px solid #4fa3ff;

}


.entry.Tina {

border-left:7px solid #ff69b4;

}



.header {

display:flex;

justify-content:space-between;

}



.delete {

background:#e74c3c;

}


</style>
