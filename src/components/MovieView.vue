<script setup>
import { ref, onMounted } from 'vue'

const API = "https://we-play-backend.onrender.com"
const TMDB_KEY = "a57b9c639bd4d8f0ec3b12594c9fbdfb"

/* =========================
   🔍 SEARCH
========================= */

const search = ref('')
const movies = ref([])
const showDropdown = ref(false)

/* =========================
   📋 MOVIE LISTS
========================= */

const watchlist = ref([])
const watching = ref([])
const watched = ref([])
const rewatch = ref([])

/* =========================
   🎬 EPISODEN TRACKER
========================= */

const openedMovie = ref(null)
const showEpisodeModal = ref(false)

const seasons = ref([])
const selectedSeason = ref(1)

/* =========================
   👤 WATCHER
========================= */

const showWatcherModal = ref(false)

const pendingMovie = ref(null)
const pendingStatus = ref(null)

const watcherOptions = [
    {
        id: "micky",
        name: "Micky",
        color: "#3b82f6",
        flag: "🔵"
    },
    {
        id: "tina",
        name: "Tina",
        color: "#ec4899",
        flag: "🩷"
    },
    {
        id: "gemeinsam",
        name: "Gemeinsam",
        color: "#166534",
        flag: "🟢"
    }
]

/* =========================
   🔍 TMDB SEARCH
========================= */

const searchMovies = async () => {

    if (!search.value.trim()) {
        movies.value = []
        showDropdown.value = false
        return
    }

    try {

        const res = await fetch(
            `https://api.themoviedb.org/3/search/multi?api_key=${TMDB_KEY}&query=${encodeURIComponent(search.value)}`
        )

        const data = await res.json()

        movies.value = data.results
            .filter(m =>
                m.media_type === "movie" ||
                m.media_type === "tv"
            )
            .slice(0, 10)

        showDropdown.value = true

    } catch (err) {

        console.log("SEARCH ERROR", err)

    }
}

/* =========================
   📥 LOAD MOVIES
========================= */

const loadMovies = async () => {

    try {

        const res = await fetch(
            `${API}/api/movies`
        )

        const data = await res.json()

        watchlist.value = []
        watching.value = []
        watched.value = []
        rewatch.value = []

        data.forEach(movie => {

            // Alte Daten absichern
            movie.type = movie.type ?? "movie"
            movie.status = movie.status ?? "watchlist"
            movie.watcher = movie.watcher ?? null
            movie.episodes = movie.episodes ?? []

            if (movie.status === "watching") {

                watching.value.push(movie)

            } else if (movie.status === "watched") {

                watched.value.push(movie)

            } else if (movie.status === "rewatch") {

                rewatch.value.push(movie)

            } else {

                watchlist.value.push(movie)

            }

        })

    } catch (err) {

        console.log(
            "LOAD ERROR",
            err
        )

    }
}

/* =========================
   ➕ ADD MOVIE
========================= */

const addMovie = async (movie) => {

    try {

        await fetch(
            `${API}/api/movies`,
            {
                method: "POST",

                headers: {
                    "Content-Type": "application/json"
                },

                body: JSON.stringify({

                    id: movie.id,

                    name:
                        movie.title ||
                        movie.name,

                    type:
                        movie.media_type,

                    image:
                        movie.poster_path
                            ? `https://image.tmdb.org/t/p/w500${movie.poster_path}`
                            : "",

                    status: "watchlist",

                    watcher: null,

                    episodes: [],

                    lastSeason: 1

                })
            }
        )

        resetSearch()

        await loadMovies()

    } catch (err) {

        console.log(
            "ADD ERROR",
            err
        )

    }
}

/* =========================
   🔄 STATUS ÄNDERN
========================= */

const updateStatus = async (movie, status) => {

    /*
     * Bei Serien zuerst fragen,
     * wer die Serie schaut.
     */

    if (movie.type === "tv") {

        pendingMovie.value = movie
        pendingStatus.value = status

        showWatcherModal.value = true

        return
    }

    /*
     * Normale Filme direkt verschieben
     */

    await performStatusChange(
        movie,
        status
    )
}

/* =========================
   🔄 STATUS SPEICHERN
========================= */

const performStatusChange = async (
    movie,
    status
) => {

    movie.status = status

    /*
     * Aus allen Listen entfernen
     */

    watchlist.value =
        watchlist.value.filter(
            m => m.id !== movie.id
        )

    watching.value =
        watching.value.filter(
            m => m.id !== movie.id
        )

    watched.value =
        watched.value.filter(
            m => m.id !== movie.id
        )

    rewatch.value =
        rewatch.value.filter(
            m => m.id !== movie.id
        )

    /*
     * In richtige Liste verschieben
     */

    if (status === "watching") {

        watching.value.push(movie)

    } else if (status === "watched") {

        watched.value.push(movie)

    } else if (status === "rewatch") {

        rewatch.value.push(movie)

    } else {

        watchlist.value.push(movie)

    }

    /*
     * Backend
     */

    try {

        await fetch(
            `${API}/api/movies/${movie.id}`,
            {
                method: "PATCH",

                headers: {
                    "Content-Type": "application/json"
                },

                body: JSON.stringify({

                    status: status,

                    watcher:
                        movie.watcher ?? null

                })
            }
        )

    } catch (err) {

        console.log(
            "STATUS ERROR",
            err
        )

    }
}

/* =========================
   👤 WATCHER AUSWÄHLEN
========================= */

const selectWatcher = async (watcher) => {

    if (!pendingMovie.value) {
        return
    }

    const movie =
        pendingMovie.value

    const status =
        pendingStatus.value

    /*
     * Person speichern
     */

    movie.watcher = watcher

    /*
     * Modal schließen
     */

    showWatcherModal.value = false

    pendingMovie.value = null
    pendingStatus.value = null

    /*
     * Status ändern
     */

    await performStatusChange(
        movie,
        status
    )
}

/* =========================
   👤 WATCHER CLASS
========================= */

const getWatcherClass = (movie) => {

    if (!movie.watcher) {
        return ""
    }

    return `watcher-${movie.watcher}`
}

/* =========================
   👤 WATCHER FLAG
========================= */

const getWatcherFlag = (movie) => {

    if (movie.watcher === "micky") {
        return "🔵"
    }

    if (movie.watcher === "tina") {
        return "🩷"
    }

    if (movie.watcher === "gemeinsam") {
        return "🟢"
    }

    return ""
}

/* =========================
   ❌ DELETE
========================= */

const deleteMovie = async (id) => {

    try {

        await fetch(
            `${API}/api/movies/${id}`,
            {
                method: "DELETE"
            }
        )

        await loadMovies()

    } catch (err) {

        console.log(
            "DELETE ERROR",
            err
        )

    }
}

/* =========================
   🔍 RESET SEARCH
========================= */

const resetSearch = () => {

    search.value = ""

    movies.value = []

    showDropdown.value = false
}

/* =========================
   🎬 SERIE ÖFFNEN
========================= */

const openMovie = async (movie) => {

    console.log(
        "OPEN MOVIE:",
        movie
    )

    if (movie.type !== "tv") {

        console.log(
            "Kein Serienobjekt"
        )

        return
    }

    openedMovie.value = movie

    showEpisodeModal.value = true

    selectedSeason.value =
        movie.lastSeason ?? 1

    await loadSeasons(movie)
}

/* =========================
   📺 STAFFELN LADEN
========================= */

const loadSeasons = async (movie) => {

    try {

        const info = await fetch(
            `https://api.themoviedb.org/3/tv/${movie.id}?api_key=${TMDB_KEY}`
        )

        const data = await info.json()

        seasons.value = []

        /*
         * Staffeln laden
         */

        for (
            let i = 1;
            i <= data.number_of_seasons;
            i++
        ) {

            const res = await fetch(
                `https://api.themoviedb.org/3/tv/${movie.id}/season/${i}?api_key=${TMDB_KEY}`
            )

            const seasonData =
                await res.json()

            seasons.value.push({

                number: i,

                episodes:
                    seasonData.episodes.map(ep => ({

                        number:
                            ep.episode_number,

                        watched:
                            movie.episodes?.some(
                                e =>
                                    e.season === i &&
                                    e.episode === ep.episode_number &&
                                    e.watched
                            ) ?? false

                    }))

            })
        }

    } catch (err) {

        console.log(
            "SEASON ERROR",
            err
        )

    }
}

/* =========================
   📺 STAFFEL WECHSELN
========================= */

const changeSeason = (seasonNumber) => {

    selectedSeason.value =
        seasonNumber

    if (openedMovie.value) {

        openedMovie.value.lastSeason =
            seasonNumber
    }
}

/* =========================
   📊 FORTSCHRITT
========================= */

const getProgress = (season) => {

    const total =
        season.episodes.length

    const done =
        season.episodes.filter(
            ep => ep.watched
        ).length

    const percent =
        total > 0
            ? Math.round(
                (done / total) * 100
            )
            : 0

    return {
        done,
        total,
        percent
    }
}

/* =========================
   ✅ STAFFEL FERTIG?
========================= */

const seasonFinished = (season) => {

    if (!season.episodes.length) {
        return false
    }

    return season.episodes.every(
        ep => ep.watched
    )
}

/* =========================
   ▶️ EPISODE TOGGLE
========================= */

const toggleEpisode = async (
    season,
    episode
) => {

    episode.watched =
        !episode.watched

    if (!openedMovie.value) {
        return
    }

    if (!openedMovie.value.episodes) {

        openedMovie.value.episodes = []

    }

    const episodes =
        openedMovie.value.episodes

    const existing =
        episodes.find(
            e =>
                e.season === season.number &&
                e.episode === episode.number
        )

    if (existing) {

        existing.watched =
            episode.watched

    } else {

        episodes.push({

            season:
                season.number,

            episode:
                episode.number,

            watched:
                episode.watched

        })
    }

    /*
     * Aktuelle Staffel speichern
     */

    openedMovie.value.lastSeason =
        season.number

    /*
     * Backend aktualisieren
     */

    try {

        await fetch(
            `${API}/api/movies/${openedMovie.value.id}`,
            {
                method: "PATCH",

                headers: {
                    "Content-Type":
                        "application/json"
                },

                body: JSON.stringify({

                    episodes:
                        openedMovie.value.episodes,

                    lastSeason:
                        openedMovie.value.lastSeason

                })
            }
        )

    } catch (err) {

        console.log(
            "EPISODE ERROR",
            err
        )

    }
}

/* =========================
   🚀 START
========================= */

onMounted(() => {

    loadMovies()

})
</script>


<template>

    <!-- =========================
         🎬 TITEL
    ========================= -->

    <h2>
        🎬 Movies & Serien
    </h2>


    <!-- =========================
         🔍 SEARCH
    ========================= -->

    <div class="search-wrapper">

        <input
            v-model="search"
            placeholder="Film oder Serie suchen..."
            @input="searchMovies"
            @focus="showDropdown = true"
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
                    :src="
                        'https://image.tmdb.org/t/p/w500'
                        + movie.poster_path
                    "
                />

                <span>
                    {{ movie.title || movie.name }}
                </span>

            </div>

        </div>

    </div>


    <!-- =========================
         📋 WATCHLIST
    ========================= -->

    <h2>
        📋 Watchlist
    </h2>


    <div class="games">

        <div
            v-for="m in watchlist"
            :key="m.id"
            class="card"
            :class="getWatcherClass(m)"
        >

            <div class="poster-wrapper">

                <img
                    v-if="m.image"
                    :src="m.image"
                    class="movie-image"
                    @click="
                        m.type === 'tv'
                            && openMovie(m)
                    "
                />

                <div
                    v-if="
                        m.type === 'tv'
                        && m.watcher
                    "
                    class="watcher-flag"
                    :class="m.watcher"
                >
                    {{ getWatcherFlag(m) }}
                </div>

            </div>


            <h3>
                {{ m.name }}
            </h3>


            <div class="buttons">

                <button
                    @click="
                        updateStatus(
                            m,
                            'watching'
                        )
                    "
                >
                    👀
                </button>


                <button
                    @click="
                        updateStatus(
                            m,
                            'watched'
                        )
                    "
                >
                    ✅
                </button>


                <button
                    @click="
                        deleteMovie(m.id)
                    "
                >
                    ❌
                </button>

            </div>

        </div>

    </div>


    <!-- =========================
         👀 WATCHING
    ========================= -->

    <h2>
        👀 Gerade am schauen
    </h2>


    <div class="games">

        <div
            v-for="m in watching"
            :key="m.id"
            class="card"
            :class="getWatcherClass(m)"
        >

            <div class="poster-wrapper">

                <img
                    v-if="m.image"
                    :src="m.image"
                    class="movie-image"
                    @click="
                        m.type === 'tv'
                            && openMovie(m)
                    "
                />

                <div
                    v-if="
                        m.type === 'tv'
                        && m.watcher
                    "
                    class="watcher-flag"
                    :class="m.watcher"
                >
                    {{ getWatcherFlag(m) }}
                </div>

            </div>


            <h3>
                {{ m.name }}
            </h3>


            <div class="buttons">

                <button
                    @click="
                        updateStatus(
                            m,
                            'watched'
                        )
                    "
                >
                    ✅
                </button>


                <button
                    @click="
                        updateStatus(
                            m,
                            'watchlist'
                        )
                    "
                >
                    ↩️
                </button>


                <button
                    @click="
                        deleteMovie(m.id)
                    "
                >
                    ❌
                </button>

            </div>

        </div>

    </div>


    <!-- =========================
         ✅ WATCHED
    ========================= -->

    <h2>
        ✅ Gesehen
    </h2>


    <div class="games">

        <div
            v-for="m in watched"
            :key="m.id"
            class="card"
            :class="getWatcherClass(m)"
        >

            <div class="poster-wrapper">

                <img
                    v-if="m.image"
                    :src="m.image"
                    class="movie-image"
                    @click="
                        m.type === 'tv'
                            && openMovie(m)
                    "
                />

                <div
                    v-if="
                        m.type === 'tv'
                        && m.watcher
                    "
                    class="watcher-flag"
                    :class="m.watcher"
                >
                    {{ getWatcherFlag(m) }}
                </div>

            </div>


            <h3>
                {{ m.name }}
            </h3>


            <div class="buttons">

                <button
                    @click="
                        updateStatus(
                            m,
                            'rewatch'
                        )
                    "
                >
                    🔄
                </button>


                <button
                    @click="
                        updateStatus(
                            m,
                            'watchlist'
                        )
                    "
                >
                    ↩️
                </button>


                <button
                    @click="
                        deleteMovie(m.id)
                    "
                >
                    ❌
                </button>

            </div>

        </div>

    </div>


    <!-- =========================
         🔄 REWATCH
    ========================= -->

    <h2>
        🔄 Rewatch
    </h2>


    <div class="games">

        <div
            v-for="m in rewatch"
            :key="m.id"
            class="card"
            :class="getWatcherClass(m)"
        >

            <div class="poster-wrapper">

                <img
                    v-if="m.image"
                    :src="m.image"
                    class="movie-image"
                    @click="
                        m.type === 'tv'
                            && openMovie(m)
                    "
                />

                <div
                    v-if="
                        m.type === 'tv'
                        && m.watcher
                    "
                    class="watcher-flag"
                    :class="m.watcher"
                >
                    {{ getWatcherFlag(m) }}
                </div>

            </div>


            <h3>
                {{ m.name }}
            </h3>


            <div class="buttons">

                <button
                    @click="
                        updateStatus(
                            m,
                            'watched'
                        )
                    "
                >
                    ✅
                </button>


                <button
                    @click="
                        updateStatus(
                            m,
                            'watchlist'
                        )
                    "
                >
                    ↩️
                </button>


                <button
                    @click="
                        deleteMovie(m.id)
                    "
                >
                    ❌
                </button>

            </div>

        </div>

    </div>


    <!-- =========================
         👤 WER SCHAUT?
    ========================= -->

    <div
        v-if="showWatcherModal"
        class="watcher-overlay"
    >

        <div class="watcher-modal">

            <h2>
                Wer schaut das?
            </h2>


            <div class="watcher-buttons">

                <button
                    class="watcher-button micky"
                    @click="
                        selectWatcher('micky')
                    "
                >

                    🔵

                    <span>
                        Micky
                    </span>

                </button>


                <button
                    class="watcher-button tina"
                    @click="
                        selectWatcher('tina')
                    "
                >

                    🩷

                    <span>
                        Tina
                    </span>

                </button>


                <button
                    class="watcher-button gemeinsam"
                    @click="
                        selectWatcher(
                            'gemeinsam'
                        )
                    "
                >

                    🟢

                    <span>
                        Gemeinsam
                    </span>

                </button>

            </div>

        </div>

    </div>


    <!-- =========================
         🎬 EPISODEN MODAL
    ========================= -->

    <div
        v-if="
            showEpisodeModal
            && openedMovie
        "
        class="episode-overlay"
    >

        <div class="episode-modal">

            <button
                class="close-button"
                @click="
                    showEpisodeModal = false
                "
            >
                ✖
            </button>


            <img
                :src="openedMovie.image"
                class="big-poster"
            />


            <h2>
                {{ openedMovie.name }}
            </h2>


            <!-- WATCHER INFO -->

            <div
                v-if="openedMovie.watcher"
                class="modal-watcher"
                :class="
                    `modal-${openedMovie.watcher}`
                "
            >

                {{ getWatcherFlag(openedMovie) }}

                {{
                    openedMovie.watcher === 'micky'
                        ? 'Micky'
                        : openedMovie.watcher === 'tina'
                            ? 'Tina'
                            : 'Gemeinsam'
                }}

            </div>


            <!-- STAFFELN -->

            <div class="season-buttons">

                <button
                    v-for="season in seasons"
                    :key="season.number"
                    @click="
                        changeSeason(
                            season.number
                        )
                    "
                    :class="{
                        active:
                            selectedSeason
                            === season.number,

                        complete:
                            seasonFinished(
                                season
                            )
                    }"
                >

                    Staffel
                    {{ season.number }}

                </button>

            </div>


            <!-- EPISODEN -->

            <div
                v-for="season in seasons"
                :key="season.number"
            >

                <div
                    v-if="
                        selectedSeason
                        === season.number
                    "
                >

                    <h3>
                        Staffel
                        {{ season.number }}
                    </h3>


                    <p>

                        {{
                            getProgress(
                                season
                            ).done
                        }}

                        /

                        {{
                            getProgress(
                                season
                            ).total
                        }}

                        Folgen

                        (
                        {{
                            getProgress(
                                season
                            ).percent
                        }}%
                        )

                    </p>


                    <div class="episodes">

                        <button
                            v-for="
                                episode
                                in season.episodes
                            "
                            :key="
                                episode.number
                            "
                            @click="
                                toggleEpisode(
                                    season,
                                    episode
                                )
                            "
                            :class="{
                                watched:
                                    episode.watched
                            }"
                        >

                            {{
                                episode.number
                            }}

                        </button>

                    </div>

                </div>

            </div>

        </div>

    </div>

</template>


<style scoped>

/* =========================
   🎬 MOVIES
========================= */

.movie-image {

    width: 100%;

    height: 180px;

    object-fit: cover;

    border-radius: 10px;

    cursor: pointer;

    transition: .2s;

}


.movie-image:hover {

    transform: scale(1.05);

}


/* =========================
   📋 GRID
========================= */

.games {

    display: grid;

    grid-template-columns:
        repeat(
            auto-fill,
            minmax(220px, 1fr)
        );

    gap: 20px;

}


/* =========================
   🃏 CARD
========================= */

.card {

    background: #1e293b;

    padding: 15px;

    border-radius: 12px;

    color: white;

    border: 3px solid transparent;

    transition: .2s;

}


/* =========================
   👤 WATCHER CARD COLORS
========================= */

.card.watcher-micky {

    border-color: #3b82f6;

}


.card.watcher-tina {

    border-color: #ec4899;

}


.card.watcher-gemeinsam {

    border-color: #166534;

}


/* =========================
   🖼️ POSTER
========================= */

.poster-wrapper {

    position: relative;

}


/* =========================
   👤 WATCHER FLAG
========================= */

.watcher-flag {

    position: absolute;

    top: 8px;

    right: 8px;

    width: 38px;

    height: 38px;

    border-radius: 50%;

    display: flex;

    align-items: center;

    justify-content: center;

    font-size: 20px;

    background: #111;

    border: 2px solid white;

    box-shadow:
        0 2px 8px
        rgba(0, 0, 0, .5);

    z-index: 5;

}


/* =========================
   🔘 BUTTONS
========================= */

.buttons {

    display: flex;

    gap: 10px;

    margin-top: 10px;

}


button {

    border: none;

    padding: 8px;

    border-radius: 8px;

    cursor: pointer;

    background: #334155;

    color: white;

}


/* =========================
   🔍 SEARCH
========================= */

.search-wrapper {

    position: relative;

    width: 500px;

    max-width: 90%;

    margin: auto;

}


.search-wrapper input {

    width: 100%;

    padding: 14px;

    background: #1e293b;

    border: none;

    border-radius: 10px;

    color: white;

    box-sizing: border-box;

}


.dropdown {

    position: absolute;

    background: #1e293b;

    width: 100%;

    z-index: 20;

    border-radius: 10px;

    overflow: hidden;

}


.dropdown-item {

    display: flex;

    gap: 10px;

    padding: 10px;

    cursor: pointer;

    align-items: center;

}


.dropdown-item:hover {

    background: #334155;

}


.dropdown-item img {

    width: 45px;

    height: 45px;

    object-fit: cover;

    border-radius: 5px;

}


/* =========================
   👤 WATCHER MODAL
========================= */

.watcher-overlay {

    position: fixed;

    inset: 0;

    background:
        rgba(0, 0, 0, .8);

    display: flex;

    align-items: center;

    justify-content: center;

    z-index: 2000;

}


.watcher-modal {

    background: #202020;

    padding: 35px;

    border-radius: 18px;

    width: 90%;

    max-width: 500px;

    color: white;

    text-align: center;

    box-shadow:
        0 10px 40px
        rgba(0, 0, 0, .7);

}


.watcher-modal h2 {

    margin-bottom: 30px;

}


.watcher-buttons {

    display: flex;

    gap: 15px;

    justify-content: center;

    flex-wrap: wrap;

}


.watcher-button {

    width: 130px;

    height: 120px;

    display: flex;

    flex-direction: column;

    align-items: center;

    justify-content: center;

    gap: 10px;

    font-size: 35px;

    transition: .2s;

}


.watcher-button:hover {

    transform: scale(1.08);

}


.watcher-button span {

    font-size: 18px;

    font-weight: bold;

}


/* MICKY */

.watcher-button.micky {

    border: 3px solid #3b82f6;

}


/* TINA */

.watcher-button.tina {

    border: 3px solid #ec4899;

}


/* GEMEINSAM */

.watcher-button.gemeinsam {

    border: 3px solid #166534;

}


/* =========================
   🎬 EPISODE OVERLAY
========================= */

.episode-overlay {

    position: fixed;

    inset: 0;

    background:
        rgba(0, 0, 0, .8);

    display: flex;

    align-items: center;

    justify-content: center;

    z-index: 1000;

}


/* =========================
   🎬 EPISODE MODAL
========================= */

.episode-modal {

    background: #202020;

    padding: 25px;

    border-radius: 15px;

    width: 90%;

    max-width: 700px;

    max-height: 90vh;

    overflow: auto;

    color: white;

    text-align: center;

}


/* =========================
   🖼️ BIG POSTER
========================= */

.big-poster {

    width: 250px;

    max-width: 80%;

    border-radius: 15px;

}


/* =========================
   ❌ CLOSE
========================= */

.close-button {

    float: right;

}


/* =========================
   📺 SEASON BUTTONS
========================= */

.season-buttons {

    display: flex;

    flex-wrap: wrap;

    gap: 10px;

    justify-content: center;

    margin: 20px;

}


.season-buttons button {

    background: #555;

}


.season-buttons .active {

    outline: 3px solid white;

}


.season-buttons .complete {

    border: 3px solid #00ff66;

}


/* =========================
   📺 EPISODES
========================= */

.episodes {

    display: flex;

    flex-wrap: wrap;

    gap: 10px;

    justify-content: center;

}


.episodes button {

    width: 45px;

    height: 45px;

}


.episodes .watched {

    background: #00b84f;

}


/* =========================
   👤 WATCHER IM MODAL
========================= */

.modal-watcher {

    display: inline-block;

    margin: 10px auto 20px;

    padding: 8px 15px;

    border-radius: 20px;

    font-weight: bold;

}


.modal-micky {

    background: #3b82f6;

}


.modal-tina {

    background: #ec4899;

}


.modal-gemeinsam {

    background: #166534;

}

</style>
