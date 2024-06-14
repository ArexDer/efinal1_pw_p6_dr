<template>
  <div id="app">
    <div class="juegoInicio">
      <div v-if="!finDelJuego && !ganar" class="tablaTotal">
        <p>Puntaje: {{ puntaje }}</p>
        <p>Intentos: {{ intentos }}</p>
      </div>
      <div v-if="!finDelJuego && !ganar" class="CuadroImagenes">
        <ImagenComponente
          v-for="card in CuadroImagenes"
          :imageSrc="card.imageSrc"
          :name="card.name"
        />
      </div>
      <button v-if="!finDelJuego && !ganar" @click="IniciarJuego">JUGAR</button>
      <div v-if="finDelJuego || ganar" :class="{ 'mensajePerdida': finDelJuego, 'blue-mensaje': ganar }">
        <p>{{ mensaje }}</p>
        <button @click="ReiniciarJuego">Nuevo Juego</button>
      </div>
    </div>
  </div>
</template>

<script>
import ImagenComponente from './components/ImagenComponente.vue';

export default {
  name: 'App',
  components: {
    ImagenComponente,
  },
  data() {
    return {
      CuadroImagenes: [
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXX' },
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXX' },
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXX' },
      ],
      intentos: 0,
      puntaje: 0,
      mensaje: '',
    };
  },
  computed: {
    finDelJuego() {
      return this.intentos >= 5 && this.puntaje < 10;
    },
    ganar() {
      return this.puntaje >= 10;
    },
  },
  methods: {
    async IniciarJuego() {
      if (this.intentos >= 5 || this.puntaje >= 10) return;

      const pokemonIds = [1, 23, 34, 73, 99];
      const pokemosAIterar = [];
      for (let i = 0; i < 3; i++) {
        const randomIndex = Math.floor(Math.random() * pokemonIds.length);
        pokemosAIterar.push(pokemonIds[randomIndex]);
      }

      const llamadoApi = pokemosAIterar.map(id => this.fetchPokemon(id));
      const resp = await Promise.all(llamadoApi);

      this.CuadroImagenes = resp.map(result => ({
        imageSrc: result.imageSrc,
        name: result.name,
      }));

      const names = resp.map(result => result.name);
      const nombrePokemon = [...new Set(names)];
      if (nombrePokemon.length === 1) {
        this.puntaje += 5;
      } else if (nombrePokemon.length === 2) {
        this.puntaje += 2;
      }

      this.intentos++;

      if (this.finDelJuego) {
        this.mensaje = 'Has utilizado tus 5 intentos. El juego ha terminado, intentalo nuevamente.';
      } else if (this.ganar) {
        this.mensaje = `Puntaje: ${this.puntaje}. Felicitaciones has ganado un premio de $10.000,00`;
      } else {
        this.mensaje = '';
      }
    },
    async fetchPokemon(id) {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
      const data = await response.json();
      return {
        imageSrc: data.sprites.other['dream_world'].front_default,
        name: data.name,
      };
    },
    ReiniciarJuego() {
      this.CuadroImagenes = [
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXXXXXX' },
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXXXXXX' },
        { imageSrc: 'https://via.placeholder.com/250', name: 'XXXXXXX' },
      ];
      this.intentos = 0;
      this.puntaje = 0;
      this.mensaje = '';
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 60px;
}

.tablaTotal {
  font-size: 20px;
  display: flex;
  gap: 80px;
}

.juegoInicio {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.CuadroImagenes {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
  gap: 20px;
}

button {
  margin: 10px;
  border: 3px solid black;
  padding: 10px 80px;
}

.mensajePerdida {
  color: red;
}

.blue-mensaje {
  color: blue;
}
</style>
