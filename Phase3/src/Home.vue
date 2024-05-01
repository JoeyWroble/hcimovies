<template>
  <!-- Displays the search bar -->
  <div class="container-fluid">
    <form class="d-flex search" @submit.prevent>
      <input v-model.trim="searchTerm" class="form-control me-2 rounded-pill" style="text-align: center;" type="search" placeholder="Search by Title">
    </form>
  </div>
  <!-- Displays the movies with their title and cover image -->
  <div class="container">
    <div class="row row-cols-1 row-cols-md-6 g-4">
      <div class="col" v-for="movie of filteredMovies" :key="movie" @click="handleTitleClick(movie)">
        <div class="card h-100">
          <img :src="imagesForMovies[movie]" class="card-img-top" :alt="`${movie} Poster`" style="max-height: 300px; object-fit: cover;">
          <div class="card-body">
            <h5 class="card-title">{{ movie }}</h5>
          </div>
        </div>
      </div>
    </div>
    <!-- Displays the modal window containing information about the film -->
    <div class="modal fade" id="movieModal" tabindex="-1">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="movieModalLabel">{{ selectedMovie.title }}</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
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
          <!-- Buttons in the footer of the modal to add movie to a list -->
          <div class="modal-footer">
              <button class="btn btn-primary" @click.stop="$emit('add-to-watchlist', selectedMovie.title, selectedMovie)">Add to Watchlist</button>
              <button class="btn btn-primary" @click.stop="$emit('add-to-mymovies', selectedMovie.title, selectedMovie)">Add to My Movies</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>

<script setup>
import { defineProps, ref, watch } from 'vue';
// Imported the modal implementation from bootstrap
import { Modal } from 'bootstrap';

const props = defineProps({ Movies: Array, data: Object });
const imagesForMovies = ref({});
const filteredMovies = ref(props.Movies);
const selectedMovie = ref({});
const searchTerm = ref('');

watch(() => props.Movies, (newMovies) => {
  updateImagesForMovies(newMovies);
  updateFilteredMovies(newMovies);
});

// Utilized ChatGPT to solve poster images not syncing with filtering error (I had no idea what was causing this or how to fix it)
function updateImagesForMovies(movies) {
  imagesForMovies.value = {};
  for (let movie of movies) {
    for (let i in props.data) {
      if (movie === props.data[i].Series_Title) {
        imagesForMovies.value[movie] = props.data[i].Poster_Link;
        break;
      }
    }
  }
}

function updateFilteredMovies(movies) {
  filteredMovies.value = movies;
}

// Sets data to variables, used to display information in modal window when the movie is clicked
function handleTitleClick(movie) {
  const movieData = props.data.find(item => item.Series_Title === movie);
  selectedMovie.value = {
    title: movieData.Series_Title,
    overview: movieData.Overview,
    poster: imagesForMovies.value[movie],
    director: movieData.Director,
    genres: movieData.Genre,
    year: movieData.Released_Year,
    cast: `${movieData.Star1}, ${movieData.Star2}, ${movieData.Star3}, ${movieData.Star4}`,
    gross: movieData.Gross,
    runtime: movieData.Runtime,
    rating: movieData.IMDB_Rating
  };
  // Creates the modal variable
  const modal = new Modal(document.getElementById('movieModal'));
  modal.show();
}

// Search bar functionality
watch(searchTerm, (newValue) => {
  filterMovies(newValue);
});

function filterMovies(searchTerm) {
  if (!searchTerm.trim()) {
    updateFilteredMovies(props.Movies);
    return;
  }
  const filtered = props.Movies.filter(movie => movie.toLowerCase().includes(searchTerm.toLowerCase()));
  updateFilteredMovies(filtered);
}

updateImagesForMovies(props.Movies);

</script>

<script> // got inspiration from online for a code that adds an item to a list on another page 
export default { // serves as a bridge for emitting events to trigger actions in its parent component
  props: ['Movies'],
  methods: {
    addToWatchlist(movie) {
      this.$emit('add-to-watchlist', movie);
    },
    addToMyMovies(movie) {
      this.$emit('add-to-my-movies', movie);
    }
  }
}
</script>

<style>
.col:hover .card {
  opacity: 0.75;
  box-shadow: 0 0 2.5px 2.5px #48abe0;
  cursor: pointer;
}

.card:hover .card-title {
  color: black;
}

.card-title {
  text-align: center;
}

.search{
  padding-block: 10px;
  padding-left: 300px;
  padding-right: 300px;
}
</style>
