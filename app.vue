<template>
  <div class="container">
    <h1>Personajes de Rick and Morty</h1>
    
    <!-- Buscador -->
    <div class="search-container">
      <input 
        type="text" 
        v-model="searchQuery" 
        placeholder="Buscar personaje..."
        @input="filterCharacters"
      />
    </div>

    <!-- Filtros -->
    <div class="filters">
      <div class="filter-group">
        <label for="status">Estado</label>
        <select id="status" v-model="selectedStatus" @change="filterCharacters">
          <option value="">Todos</option>
          <option value="Alive">Alive</option>
          <option value="Dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select>
      </div>

      <div class="filter-group">
        <label for="species">Especie</label>
        <select id="species" v-model="selectedSpecies" @change="filterCharacters">
          <option value="">Todos</option>
          <option value="Human">Human</option>
          <option value="Alien">Alien</option>
          <option value="Mythological Creature">Mythological Creature</option>
          <option value="Robot">Robot</option>
        </select>
      </div>

      <div class="filter-group">
        <label for="gender">Género</label>
        <select id="gender" v-model="selectedGender" @change="filterCharacters">
          <option value="">Todos</option>
          <option value="Male">Male</option>
          <option value="Female">Female</option>
          <option value="Genderless">Genderless</option>
          <option value="unknown">Unknown</option>
        </select>
      </div>
    </div>

    <div class="character-list">
      <NuxtLink
        v-for="character in filteredCharacters"
        :key="character.id"
        :to="`/character/${character.id}`"
      >
        <CharacterCard :character="character" />
      </NuxtLink>
    </div>
    
    <p v-if="loading">Cargando personajes...</p>
    <p v-if="error">{{ error }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import CharacterCard from '@/components/CharacterCard.vue';

const characters = ref([]);
const filteredCharacters = ref([]);
const searchQuery = ref('');
const loading = ref(true);
const error = ref(null);
const selectedStatus = ref(''); // Filtro de estado seleccionado
const selectedSpecies = ref(''); // Filtro de especie seleccionado
const selectedGender = ref(''); // Filtro de género seleccionado

onMounted(async () => {
  try {
    const response = await axios.get('https://rickandmortyapi.com/api/character');
    characters.value = response.data.results;
    filteredCharacters.value = characters.value; // Inicialmente, se muestran todos los personajes
  } catch (err) {
    error.value = 'Error al cargar los personajes';
    console.error('Error fetching characters:', err);
  } finally {
    loading.value = false;
  }
});

// Filtrar personajes basados en la consulta de búsqueda y los filtros seleccionados
const filterCharacters = () => {
  filteredCharacters.value = characters.value.filter(character => {
    const matchesSearch = character.name.toLowerCase().includes(searchQuery.value.toLowerCase());
    const matchesStatus = selectedStatus.value ? character.status === selectedStatus.value : true;
    const matchesSpecies = selectedSpecies.value ? character.species === selectedSpecies.value : true;
    const matchesGender = selectedGender.value ? character.gender === selectedGender.value : true;

    return matchesSearch && matchesStatus && matchesSpecies && matchesGender; // Debe coincidir con todos los criterios
  });
};
</script>

<style scoped>
/* Contenedor principal */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f7f9fc; /* Color de fondo suave */
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

/* Títulos */
h1 {
  text-align: center;
  color: #333;
  font-family: 'Arial', sans-serif;
  margin-bottom: 20px;
}

/* Contenedor del buscador */
.search-container {
  display: flex;
  justify-content: center; /* Centra el input */
  margin-bottom: 20px; /* Espacio debajo del buscador */
}

/* Estilo del input */
input {
  padding: 15px;
  width: 100%;
  max-width: 400px; /* Máximo ancho del input */
  border: 2px solid #007BFF;
  border-radius: 25px;
  font-size: 16px;
  transition: border-color 0.3s;
}

input:focus {
  border-color: #0056b3; /* Color del borde al enfocar */
  outline: none; /* Sin outline */
}

/* Filtros */
.filters {
  display: flex;
  justify-content: space-between; /* Espacio entre los filtros */
  margin-bottom: 20px;
}

/* Grupo de filtro */
.filter-group {
  display: flex;
  flex-direction: column;
  flex: 1; /* Permite que los grupos de filtros se distribuyan uniformemente */
  margin-right: 10px; /* Espacio a la derecha de cada grupo */
}

.filter-group:last-child {
  margin-right: 0; /* Eliminar margen derecho del último grupo */
}

label {
  margin-bottom: 5px;
  font-weight: bold;
  color: #007BFF; /* Color azul para las etiquetas */
}

/* Estilo de los selects */
select {
  padding: 10px;
  border: 2px solid #007BFF;
  border-radius: 5px;
  font-size: 16px;
  transition: border-color 0.3s;
}

select:focus {
  border-color: #0056b3; /* Color del borde al enfocar */
  outline: none; /* Sin outline */
}

/* Lista de personajes */
.character-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.character-list a {
  text-decoration: none; /* Sin subrayado en los enlaces */
}

.character-card {
  margin: 10px;
  border: 2px solid #e1e1e1;
  border-radius: 8px;
  overflow: hidden; /* Para que no se salga el contenido */
  background-color: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s; /* Efecto de animación */
}

.character-card:hover {
  transform: translateY(-5px); /* Levanta un poco la tarjeta al pasar el mouse */
}

/* Cargando y error */
p {
  text-align: center;
  color: #666; /* Color de texto sutil */
}

/* Media queries para responsividad */
@media (max-width: 768px) {
  input {
    max-width: 100%; /* Ancho completo en pantallas pequeñas */
  }
  .filters {
    flex-direction: column; /* Cambiar a columna en pantallas pequeñas */
  }
  .filter-group {
    margin-right: 0; /* Eliminar margen derecho en pantallas pequeñas */
  }
}
</style>
