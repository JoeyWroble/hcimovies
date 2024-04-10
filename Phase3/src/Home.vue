<script setup>
    const props = defineProps({
        Movies: Array
    });
</script>
<template>
    <div v-for="movie of props.Movies">
        <p>{{ movie }}</p>
    </div>
</template>