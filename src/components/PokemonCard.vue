<template>
    <div class="card">
      <div class="card__content-img">
        <img class="img-quien" src="../assets/whopokemon.jpg"  alt="¿Quién es este Pokémon?" />
        <img
          ref="cardImage"
          :src="pokemon.sprites?.front_default"
          :alt="pokemon?.name"
          class="img-api"
          loading="lazy"
          :style="{ filter: mostrado ? 'none' : 'brightness(0)' }"
          :class="{ 'brillo-descubierto': adivinado }"
        />
        <div v-if="error" class="error-overlay">
          <img class="img-error" src="../assets/5219070.png" width="120" height="120" alt="Error" />
        </div>
      </div>
      <h3 v-if="mostrado" class="pokemon-nombre">{{ pokemon.name }}</h3>
      <div class="card-content" :style="{ display: mostrado ? 'none' : 'block' }">
        <input
          ref="pokemonInput"
          type="text"
          placeholder="Escribe el nombre del pokemon incógnito"
          class="card-input"
          v-model="pokemonInput"
          @keyup.enter="descubrir"
          @keyup.esc="omitir"
          autocapitalize="none"
          autocomplete="off"
        />
        <div class="content-button">
          <button class="button-descubrir" @click="descubrir">Descubrir</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        pokemonInput: "",
        mostrado: false,
        adivinado: false,
        error: false,
      };
    },
    props: {
      pokemon: {
        type: Object,
        required: true,
      },
    },
    methods: {
      descubrir() {
        if (this.pokemonInput.toLowerCase() === this.pokemon.name) {
          this.adivinado = true;
          this.mostrado = true;
          this.error = false;
          this.$nextTick(() => {
            this.$refs.cardImage.classList.add("brillo-descubierto");
            setTimeout(() => {
              this.$refs.cardImage.classList.remove("brillo-descubierto");
            }, 1500);
          });
          setTimeout(() => {
            this.$emit("pokemonAdivinado", this.pokemon); // Emitimos el Pokémon adivinado al padre
          }, 2500);
        } else {
          this.error = true;
          setTimeout(() => {
            this.error = false;
          }, 3000);
        }
      },
      omitir() {
        this.mostrado = true;
        this.adivinado = false;
        setTimeout(() => {
          this.$emit("pokemonOmitido", this.pokemon); // Emitimos el Pokémon omitido al padre
        }, 2000);
      },
    },
  };
  </script>
  
  <style scoped>
  .card {
    padding: 15px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-width: 100%;
    background-color: #ffd900;
    border-radius: 5px;
    box-shadow: 0px -10px 15px -10px grey, 0px 10px 15px -10px grey;
  }
  
  .card__content-img {
    width: 100%;
    position: relative;
    margin: 0 auto;
  }
  
  .img-quien {
    width: 100%;
    height: auto;
    border-radius: 5px;
  }
  
  .img-api {
    width: 100px; /* Reducir el tamaño */
    height: auto;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  
  .card-input {
    width: 100%;
    padding: 10px;
    margin-top: 20px;
    border-radius: 5px;
    box-sizing: border-box;
  }
  
  button {
    margin-top: 10px;
    width: 100%;
    height: 45px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .pokemon-nombre {
    color: white;
    font-size: 18px;
    font-weight: bold;
    text-align: center;
  }
  </style>
  