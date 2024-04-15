<script setup>
import { ref } from 'vue';
import { Modal } from 'bootstrap';

const props = defineProps({ mymovies: Array });
const selectedMovie = ref({});

function handleTitleClick(movie) {
  selectedMovie.value = movie;
  const modal = new Modal(document.getElementById('movieModal'));
  modal.show();
}
function removeFromMyMovies(movie) {
  const index = props.mymovies.findIndex(m => m.title === movie.title);
  if (index !== -1) {
    props.mymovies.splice(index, 1);
  }
}
</script>

<template>
  <div>
    <h2>My Movies</h2>
    <div class="row row-cols-1 row-cols-md-6 g-4">
      <div class="col" v-for="movie in props.mymovies" :key="movie.title" @click="handleTitleClick(movie)">
        <div class="card h-100">
          <img :src="movie.poster" class="card-img-top" :alt="`${movie.title} Poster`" style="max-height: 300px; object-fit: cover;">
          <div class="card-body">
            <h5 class="card-title">{{ movie.title }}</h5>
            <button class="btn btn-danger btn-sm" @click.stop="removeFromMyMovies(movie)">Remove</button>
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
          <div class="modal-body">
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