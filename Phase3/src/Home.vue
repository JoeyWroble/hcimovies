<script setup>
    const props = defineProps({
        movieTitles: Array
    });
</script>
<template>
    <div v-for="movie of props.movieTitles">
        <p>{{ movie }}</p>
    </div>
</template>