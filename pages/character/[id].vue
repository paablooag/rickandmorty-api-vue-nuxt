<template>
    <div v-if="character">
      <h2>{{ character.name }}</h2>
      <img :src="character.image" alt="Character Image" />
      <p><strong>Status:</strong> {{ character.status }}</p>
      <p><strong>Species:</strong> {{ character.species }}</p>
      <p><strong>Gender:</strong> {{ character.gender }}</p>
      <p><strong>Origin:</strong> {{ character.origin.name }}</p>
      <NuxtLink to="/">Volver a la lista</NuxtLink>
    </div>
    <p v-else>Cargando detalles del personaje...</p>
    <p v-if="error">{{ error }}</p>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import { useRoute } from 'vue-router';
  import axios from 'axios';
  
  const route = useRoute();
  const character = ref(null);
  const error = ref(null);
  
  // Obtener el ID del personaje desde la ruta
  const characterId = route.params.id;
  
  // Cargar los detalles del personaje desde la API
  onMounted(async () => {
    try {
      const response = await axios.get(`https://rickandmortyapi.com/api/character/${characterId}`);
      character.value = response.data;
    } catch (err) {
      error.value = 'Error al cargar los detalles del personaje';
      console.error('Error fetching character details:', err);
    }
  });
  </script>
  