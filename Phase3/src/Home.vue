<template>
<div class="container">
  <div class="row row-cols-1 row-cols-md-6 g-4">
    <div class="col" v-for="movie of props.Movies" :key="movie" @click="handleTitleClick(movie)">
      <div class="card h-100">
        <img :src="imagesForMovies[props.Movies.indexOf(movie)]" class="card-img-top" :alt="`${movie} Poster`" style="max-height: 300px; object-fit: cover;">
        <div class="card-body">
          <h5 class="card-title">{{ movie }}</h5>
        </div>
      </div>
    </div>
  </div>

  <div class="modal fade" id="movieModal" tabindex="-1" aria-labelledby="movieModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="movieModalLabel">Movie Title</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <p><strong>Director:</strong> </p>
          <p><strong>Genre(s):</strong> </p>
          <p><strong>Rating:</strong> </p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
  
  <script setup>
  import { defineProps, ref, watch } from 'vue';
  import { Modal } from 'bootstrap';
  
  const props = defineProps({
    Movies: Array,
    data: Object
  });
  
  const imagesForMovies = ref([])
  for (let m of props.Movies) {
    for (let i in props.data) {
        if (m == props.data[i].Series_Title) {
            imagesForMovies.value.push(props.data[i].Poster_Link)
        }
    }
  }



  watch(props, () => {
  imagesForMovies.value = []; // Clear previous images
  for (let movie of props.Movies) {
    for (let i in props.data) {
      if (movie === props.data[i].Series_Title) {
        imagesForMovies.value.push(props.data[i].Poster_Link);
        break; // Stop searching after finding the image
      }
    }
  }
}, { deep: true }); // Watch for changes in nested objects
  function handleTitleClick(movie) {
    const modal = new Modal(document.getElementById('movieModal'));
    const modalTitle = document.getElementById('movieModalLabel');
    modalTitle.textContent = movie;
  
    modal.show();
  }
  </script>

  <style>

  .col:hover .card{
    opacity: 0.75;
    box-shadow: 0 0 0 2px blue;
    cursor: pointer;
  }

  .card:hover .card-title {
    color: black;
}

    .card-title {
        text-align: center;
    }

</style>