<script setup>
import { ref } from 'vue';
import { Modal } from 'bootstrap';

const props = defineProps({ watchlist: Array });
const selectedMovie = ref({});

function handleTitleClick(movie) {
  selectedMovie.value = movie;
  const modal = new Modal(document.getElementById('movieModal'));
  modal.show();
}

function removeFromWatchlist(movie) { // remove function
  const index = props.watchlist.findIndex(m => m.title === movie.title);
  if (index !== -1) {
    props.watchlist.splice(index, 1);
  }
}
</script>

<template>
    <div class="container-fluid">
    <form class="d-flex search" @submit.prevent>
      <input v-model.trim="searchTerm" class="form-control me-2 rounded-pill" style="text-align: center;" type="search" placeholder="Search by Title">
    </form>
  </div>
  <div>
    <h2>Your Watchlist</h2>
    <div class="row row-cols-1 row-cols-md-6 g-4">
      <div class="col" v-for="movie in props.watchlist" :key="movie.title" @click="handleTitleClick(movie)">
        <div class="card h-100">
          <img :src="movie.poster" class="card-img-top" :alt="`${movie.title} Poster`" style="max-height: 300px; object-fit: cover;">
          <div class="card-body">
          <h5 class="card-title">{{ movie.title }}</h5>
          <button class="btn btn-danger btn-sm" @click.stop="removeFromWatchlist(movie)">Remove</button>
          </div>
        </div>
      </div>
    </div>

    <div class="modal fade" id="movieModal" tabindex="-1" aria-labelledby="movieModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="movieModalLabel">{{ selectedMovie.title }}</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body"> <!-- displays movie info and card -->
            <img :src="selectedMovie.poster" class="img-fluid mb-3">
            <p><strong>Description:</strong> {{ selectedMovie.overview }}</p>
            <p><strong>Genre(s):</strong> {{ selectedMovie.genres }}</p>
            <p><strong>Cast:</strong> {{ selectedMovie.cast }}</p>
            <p><strong>Director:</strong> {{ selectedMovie.director }}</p>
            <p><strong>Runtime:</strong> {{ selectedMovie.runtime }}</p>
            <p><strong>Release Year:</strong> {{ selectedMovie.year }}</p>
            <p><strong>Gross Revenue:</strong> ${{ selectedMovie.gross }} USD</p>
            <p><strong>IMDB Rating:</strong> {{ selectedMovie.rating }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>