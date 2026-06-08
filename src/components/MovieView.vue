<template>
  <h2>🎬 Movies & Serien</h2>

  <!-- SEARCH -->
  <div class="search-wrapper">
    <input
      v-model="search"
      placeholder="Film oder Serie suchen..."
      @input="searchMovies"
      @focus="showDropdown = true"
    />

    <div v-if="showDropdown && movies.length" class="dropdown">
      <div
        v-for="movie in movies"
        :key="movie.id"
        class="dropdown-item"
        @click="addMovie(movie)"
      >
        <img
          v-if="movie.poster_path"
          :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path"
        />
        <span>{{ movie.title || movie.name }}</span>
      </div>
    </div>
  </div>

  <!-- WATCHLIST -->
  <h2>📋 Watchlist</h2>
  <div class="games">
    <div v-for="m in watchlist" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="updateStatus(m, 'watched')">✅ Gesehen</button>
        <button class="rewatch" @click="updateStatus(m, 'rewatch')">🔄 Rewatch</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>

  <!-- WATCHED -->
  <h2>✅ Gesehen</h2>
  <div class="games">
    <div v-for="m in watched" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="rewatch" @click="updateStatus(m, 'rewatch')">🔄 Rewatch</button>
        <button class="back" @click="updateStatus(m, 'watchlist')">↩️ Zurück</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>

  <!-- REWATCH -->
  <h2>🔄 Rewatch</h2>
  <div class="games">
    <div v-for="m in rewatch" :key="m.id" class="card">
      <img v-if="m.image" :src="m.image" />
      <h3>{{ m.name }}</h3>

      <div class="buttons">
        <button class="watched" @click="updateStatus(m, 'watched')">✅ Gesehen</button>
        <button class="back" @click="updateStatus(m, 'watchlist')">↩️ Zurück</button>
        <button class="delete" @click="deleteMovie(m.id)">❌</button>
      </div>
    </div>
  </div>
</template>
