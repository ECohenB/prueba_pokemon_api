<template>
  <div class="contenedor-img mb-4 col-12">
    <img class="img-titulo img-fluid" src="./assets/pokemon.jpg" width="50%" height="50%" alt="titulo-pokemon">
  </div>
  <h2>¿Quién es ese Pokémon?</h2>

  <div class="pokemon-grid">
    <div v-for="pokemon in pokemons" :key="pokemon.id">
      <PokemonCard 
        :pokemon="pokemon" 
        @pokemonAdivinado="incrementarContador"
        @pokemonOmitido="incrementarContador"
      />
    </div>
  </div>

  <div class="pokemon-descubiertos">
    <p>Pokemones descubiertos: {{ countPokemons }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import PokemonCard from './components/PokemonCard.vue';
import axios from 'axios';

const pokemons = ref([]);
const countPokemons = ref(0);

// Obtener la lista de 20 Pokémon
onMounted(async () => {
  try {
    const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=20');
    const pokemonUrls = response.data.results.map(pokemon => pokemon.url);

    // Obtener los detalles de cada Pokémon
    const pokemonDetails = await Promise.all(
      pokemonUrls.map(url => axios.get(url).then(res => res.data))
    );

    pokemons.value = pokemonDetails;
  } catch (error) {
    console.error('Error al obtener los Pokémon:', error);
  }
});

// Método para incrementar el contador de Pokémon descubiertos
function incrementarContador() {
  countPokemons.value++;
}
</script>

<style scoped>
.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr); /* 4 columnas */
  gap: 20px; /* Espacio entre las tarjetas */
  padding: 10px;
}

.pokemon-descubiertos {
  margin-top: 20px;
  font-size: 1.2rem;
  font-weight: bold;
}
</style>
