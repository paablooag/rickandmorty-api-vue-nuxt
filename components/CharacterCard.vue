<template>
    <div class="character-card">
      <img :src="character.image" :alt="character.name" />
      <h2>{{ character.name }}</h2>
      <p>Estado: {{ character.status }}</p>
      <p>Especie: {{ character.species }}</p>
      <p>Género: {{ character.gender }}</p>
      <p>Origen: {{ character.origin.name }}</p>
      <p>Ubicación: {{ character.location.name }}</p>
    </div>
  </template>
  
  <script setup>
  defineProps({
    character: {
      type: Object,
      required: true
    }
  });
  </script>
  
  <style scoped>
  .character-card {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 16px;
    margin: 10px;
    text-align: center;
    width: 200px; /* Ajusta el tamaño según sea necesario */
  }
  .character-card img {
    width: 100%; /* La imagen ocupará todo el ancho del card */
    height: auto;
  }
  </style>
  