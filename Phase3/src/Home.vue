<template>
    <div>
      <div v-for="movie of props.Movies" :key="movie" @click="handleTitleClick(movie)">
        <span>{{ movie }}</span>
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
  import { defineProps } from 'vue';
  import { Modal } from 'bootstrap';
  
  const props = defineProps({
    Movies: Array
  });
  
  function handleTitleClick(movie) {
    const modal = new Modal(document.getElementById('movieModal'));
    const modalTitle = document.getElementById('movieModalLabel');
    modalTitle.textContent = movie;
  
    modal.show();
  }
  </script>