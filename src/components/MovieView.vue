<script setup>
import { ref, onMounted } from 'vue'


const API = "https://we-play-backend.onrender.com"

const TMDB_KEY = "a57b9c639bd4d8f0ec3b12594c9fbdfb"



const search = ref('')
const movies = ref([])


const watchlist = ref([])
const watching = ref([])
const watched = ref([])
const rewatch = ref([])


const showDropdown = ref(false)



/* =========================
 🎬 EPISODEN TRACKER
========================= */

const openedMovie = ref(null)
const showEpisodeModal = ref(false)

const seasons = ref([])
const selectedSeason = ref(1)





/* =========================
 🔍 TMDB SEARCH
========================= */

const searchMovies = async()=>{


    if(!search.value.trim()){

        movies.value=[]

        return

    }



    const res = await fetch(
        `https://api.themoviedb.org/3/search/multi?api_key=${TMDB_KEY}&query=${search.value}`
    )


    const data = await res.json()



    movies.value =
        data.results
        .filter(m =>
            m.media_type === "movie" ||
            m.media_type === "tv"
        )
        .slice(0,10)



    showDropdown.value=true

}






/* =========================
 📥 LOAD MOVIES
========================= */

const loadMovies = async()=>{


try{


    const res =
    await fetch(`${API}/api/movies`)



    if(!res.ok){

        console.log("Backend Fehler")

        return

    }



    const data =
    await res.json()



    watchlist.value=[]
    watching.value=[]
    watched.value=[]
    rewatch.value=[]



    data.forEach(movie=>{


        // alte Daten fixen
        movie.type =
        movie.type ?? "movie"



        const status =
        movie.status ?? "watchlist"




        if(status==="watching")

            watching.value.push(movie)



        else if(status==="watched")

            watched.value.push(movie)



        else if(status==="rewatch")

            rewatch.value.push(movie)



        else

            watchlist.value.push(movie)



    })



}
catch(err){

    console.log(
        "LOAD ERROR:",
        err
    )

}


}







/* =========================
 ➕ ADD MOVIE
========================= */

const addMovie = async(movie)=>{


    await fetch(
        `${API}/api/movies`,
        {


            method:"POST",


            headers:{
                "Content-Type":"application/json"
            },


            body:JSON.stringify({


                id:movie.id,


                name:
                movie.title ||
                movie.name,



                type:
                movie.media_type,



                image:
                movie.poster_path
                ?
                `https://image.tmdb.org/t/p/w500${movie.poster_path}`
                :
                "",



                status:"watchlist"


            })

        }
    )



    resetSearch()


    loadMovies()


}






/* =========================
 🔄 STATUS ÄNDERN
========================= */

const updateStatus = async(movie,status)=>{


    movie.status=status



    watchlist.value =
    watchlist.value.filter(
        m=>m.id!==movie.id
    )


    watching.value =
    watching.value.filter(
        m=>m.id!==movie.id
    )


    watched.value =
    watched.value.filter(
        m=>m.id!==movie.id
    )


    rewatch.value =
    rewatch.value.filter(
        m=>m.id!==movie.id
    )




    if(status==="watching")

        watching.value.push(movie)



    else if(status==="watched")

        watched.value.push(movie)



    else if(status==="rewatch")

        rewatch.value.push(movie)



    else

        watchlist.value.push(movie)






    await fetch(
        `${API}/api/movies/${movie.id}`,
        {


            method:"PATCH",


            headers:{
                "Content-Type":"application/json"
            },


            body:JSON.stringify({

                status

            })


        }
    )


}






/* =========================
 ❌ DELETE
========================= */

const deleteMovie = async(id)=>{


    await fetch(
        `${API}/api/movies/${id}`,
        {

            method:"DELETE"

        }
    )


    loadMovies()

}







/* =========================
 🔍 RESET
========================= */

const resetSearch=()=>{


    search.value=""


    movies.value=[]


    showDropdown.value=false


}






/* =========================
 🎬 SERIE ÖFFNEN
========================= */

const openMovie = async(movie)=>{


    console.log(
        "OPEN:",
        movie
    )



    if(movie.type !== "tv"){

        return

    }



    openedMovie.value = movie


    showEpisodeModal.value=true



    selectedSeason.value =
    movie.lastSeason ?? 1




    await loadSeasons(movie)


}






/* =========================
 📺 STAFFELN LADEN
========================= */

const loadSeasons = async(movie)=>{


    const info =
    await fetch(
        `https://api.themoviedb.org/3/tv/${movie.id}?api_key=${TMDB_KEY}`
    )



    const data =
    await info.json()



    seasons.value=[]




    for(
        let i=1;
        i<=data.number_of_seasons;
        i++
    ){


        const res =
        await fetch(
            `https://api.themoviedb.org/3/tv/${movie.id}/season/${i}?api_key=${TMDB_KEY}`
        )



        const seasonData =
        await res.json()




        seasons.value.push({


            number:i,



            episodes:


            seasonData.episodes.map(ep=>({



                number:
                ep.episode_number,



                watched:

                movie.episodes?.some(e=>


                    e.season===i &&

                    e.episode===ep.episode_number &&

                    e.watched


                ) ?? false



            }))


        })



    }


}
/* =========================
 📌 STAFFEL SPEICHERN
========================= */

const changeSeason = async(season)=>{


    selectedSeason.value = season



    if(!openedMovie.value)
        return



    openedMovie.value.lastSeason = season



    await fetch(
        `${API}/api/movies/${openedMovie.value.id}/season`,
        {

            method:"PATCH",

            headers:{
                "Content-Type":"application/json"
            },


            body:JSON.stringify({

                lastSeason:season

            })

        }
    )


}







/* =========================
 ✅ EPISODE TOGGLE
========================= */

const toggleEpisode = async(season,episode)=>{


    episode.watched =
    !episode.watched



    await fetch(
        `${API}/api/movies/${openedMovie.value.id}/episodes`,
        {


            method:"PATCH",


            headers:{
                "Content-Type":"application/json"
            },


            body:JSON.stringify({

                season:season.number,

                episode:episode.number,

                watched:episode.watched

            })


        }
    )


}






/* =========================
 📊 FORTSCHRITT
========================= */

const getProgress=(season)=>{


    const total =
    season.episodes.length



    const done =
    season.episodes.filter(
        e=>e.watched
    ).length



    return {


        done,


        total,


        percent:

        total
        ?
        Math.round(
            done / total * 100
        )
        :
        0


    }


}







const seasonFinished=(season)=>{


    return getProgress(season).percent === 100


}






onMounted(loadMovies)

</script>





<template>


<h2>🎬 Movies & Serien</h2>




<div class="search-wrapper">


<input

v-model="search"

placeholder="Film oder Serie suchen..."

@input="searchMovies"

@focus="showDropdown=true"

/>





<div

v-if="showDropdown && movies.length"

class="dropdown"

>


<div

v-for="movie in movies"

:key="movie.id"

class="dropdown-item"

@click="addMovie(movie)"

>



<img

v-if="movie.poster_path"

:src="'https://image.tmdb.org/t/p/w500'+movie.poster_path"

/>



<span>

{{movie.title || movie.name}}

</span>



</div>



</div>


</div>






<!-- WATCHLIST -->


<h2>📋 Watchlist</h2>


<div class="games">



<div

v-for="m in watchlist"

:key="m.id"

class="card"

>



<img

v-if="m.image"

:src="m.image"

@click="openMovie(m)"

class="movie-image"

/>



<h3>{{m.name}}</h3>




<div class="buttons">


<button

@click="updateStatus(m,'watching')"

>

👀

</button>



<button

@click="updateStatus(m,'watched')"

>

✅

</button>



<button

@click="deleteMovie(m.id)"

>

❌

</button>


</div>


</div>



</div>





<!-- WATCHING -->


<h2>👀 Gerade am schauen</h2>


<div class="games">



<div

v-for="m in watching"

:key="m.id"

class="card"

>



<img

v-if="m.image"

:src="m.image"

@click="m.type === 'tv' && openMovie(m)"

class="movie-image"

/>



<h3>{{m.name}}</h3>



<div class="buttons">


<button

@click="updateStatus(m,'watched')"

>

✅

</button>



<button

@click="updateStatus(m,'watchlist')"

>

↩️

</button>



<button

@click="deleteMovie(m.id)"

>

❌

</button>



</div>



</div>



</div>
<template>

<h2>🎬 Movies & Serien</h2>


<div class="search-wrapper">

<input
v-model="search"
placeholder="Film oder Serie suchen..."
@input="searchMovies"
@focus="showDropdown=true"
/>



<div
v-if="showDropdown && movies.length"
class="dropdown"
>


<div
v-for="movie in movies"
:key="movie.id"
class="dropdown-item"
@click="addMovie(movie)"
>


<img
v-if="movie.poster_path"
:src="'https://image.tmdb.org/t/p/w500'+movie.poster_path"
/>


<span>
{{movie.title || movie.name}}
</span>


</div>


</div>

</div>




<!-- WATCHLIST -->

<h2>📋 Watchlist</h2>


<div class="games">

<div
v-for="m in watchlist"
:key="m.id"
class="card"
>


<img
v-if="m.image"
:src="m.image"
class="movie-image"
@click="openMovie(m)"
/>


<h3>
{{m.name}}
</h3>


<div class="buttons">


<button
@click="updateStatus(m,'watching')">
👀
</button>


<button
@click="updateStatus(m,'watched')">
✅
</button>


<button
@click="deleteMovie(m.id)">
❌
</button>


</div>


</div>

</div>






<!-- WATCHING -->

<h2>👀 Gerade am schauen</h2>


<div class="games">

<div
v-for="m in watching"
:key="m.id"
class="card"
>


<img
v-if="m.image"
:src="m.image"
class="movie-image"
@click="openMovie(m)"
/>


<h3>
{{m.name}}
</h3>


<div class="buttons">


<button
@click="updateStatus(m,'watched')">
✅
</button>


<button
@click="updateStatus(m,'watchlist')">
↩️
</button>


<button
@click="deleteMovie(m.id)">
❌
</button>


</div>


</div>

</div>







<!-- WATCHED -->

<h2>✅ Gesehen</h2>


<div class="games">

<div
v-for="m in watched"
:key="m.id"
class="card"
>


<img
v-if="m.image"
:src="m.image"
class="movie-image"
@click="openMovie(m)"
/>


<h3>
{{m.name}}
</h3>


<div class="buttons">


<button
@click="updateStatus(m,'rewatch')">
🔄
</button>


<button
@click="updateStatus(m,'watchlist')">
↩️
</button>


<button
@click="deleteMovie(m.id)">
❌
</button>


</div>


</div>

</div>







<!-- REWATCH -->

<h2>🔄 Rewatch</h2>


<div class="games">


<div
v-for="m in rewatch"
:key="m.id"
class="card"
>


<img
v-if="m.image"
:src="m.image"
class="movie-image"
@click="openMovie(m)"
/>


<h3>
{{m.name}}
</h3>


<div class="buttons">


<button
@click="updateStatus(m,'watched')">
✅
</button>


<button
@click="updateStatus(m,'watchlist')">
↩️
</button>


<button
@click="deleteMovie(m.id)">
❌
</button>


</div>


</div>


</div>









<!-- EPISODEN MODAL -->


<div
v-if="showEpisodeModal && openedMovie"
class="episode-overlay"
>


<div class="episode-modal">


<button
class="close-button"
@click="showEpisodeModal=false">
✖
</button>



<img
:src="openedMovie.image"
class="big-poster"
/>


<h2>
{{openedMovie.name}}
</h2>





<div class="season-buttons">


<button
v-for="season in seasons"
:key="season.number"
@click="changeSeason(season.number)"
:class="{
active:selectedSeason===season.number,
complete:seasonFinished(season)
}"
>

Staffel {{season.number}}

</button>


</div>





<div
v-for="season in seasons"
:key="season.number"
>


<div
v-if="selectedSeason===season.number"
>


<h3>
Staffel {{season.number}}
</h3>


<p>

{{getProgress(season).done}}
/
{{getProgress(season).total}}

Folgen

({{getProgress(season).percent}}%)

</p>




<div class="episodes">


<button
v-for="episode in season.episodes"
:key="episode.number"
@click="toggleEpisode(season,episode)"
:class="{
watched:episode.watched
}"
>

{{episode.number}}

</button>


</div>



</div>


</div>



</div>


</div>


</template>





<style scoped>


.movie-image{

width:100%;

height:180px;

object-fit:cover;

border-radius:10px;

cursor:pointer;

transition:.2s;

}


.movie-image:hover{

transform:scale(1.05);

}



.games{

display:grid;

grid-template-columns:
repeat(auto-fill,minmax(220px,1fr));

gap:20px;

}



.card{

background:#1e293b;

padding:15px;

border-radius:12px;

color:white;

}



.buttons{

display:flex;

gap:10px;

margin-top:10px;

}


button{

border:none;

padding:8px;

border-radius:8px;

cursor:pointer;

background:#334155;

color:white;

}





.search-wrapper{

position:relative;

width:500px;

margin:auto;

}


.search-wrapper input{

width:100%;

padding:14px;

background:#1e293b;

border:none;

border-radius:10px;

color:white;

}





.dropdown{

position:absolute;

background:#1e293b;

width:100%;

z-index:20;

}



.dropdown-item{

display:flex;

gap:10px;

padding:10px;

cursor:pointer;

}



.dropdown-item:hover{

background:#334155;

}



.dropdown-item img{

width:45px;

height:45px;

object-fit:cover;

}




.episode-overlay{

position:fixed;

inset:0;

background:rgba(0,0,0,.8);

display:flex;

align-items:center;

justify-content:center;

z-index:1000;

}



.episode-modal{

background:#202020;

padding:25px;

border-radius:15px;

width:90%;

max-width:700px;

max-height:90vh;

overflow:auto;

color:white;

text-align:center;

}



.big-poster{

width:250px;

border-radius:15px;

}



.close-button{

float:right;

}



.season-buttons{

display:flex;

flex-wrap:wrap;

gap:10px;

justify-content:center;

margin:20px;

}



.season-buttons button{

background:#555;

}



.season-buttons .active{

outline:3px solid white;

}



.season-buttons .complete{

border:3px solid #00ff66;

}



.episodes{

display:flex;

flex-wrap:wrap;

gap:10px;

justify-content:center;

}



.episodes button{

width:45px;

height:45px;

}



.episodes .watched{

background:#00b84f;

}



</style>
