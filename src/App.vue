<template>
  <div class="padre" style="text-transform: uppercase;">
    <div class="Parte1">
      <img class="pokedex" src="./Pok-dex_logo.png" alt=""><br>
      <!-- <img class="gif" src="./d4ebfde8dc49952ad4ee360d8012cb-unscreen.gif" alt=""> -->
    </div>
    <input class="buscador" type="text" placeholder="Nombre o id del Pokemon" v-model="name" @keyup.enter="traer" />
    <button @click="traer">Buscar</button><br><br>

    <div v-if="busquedaRealizada" class="caracteristicas">
      <div class="uno">
        <div class="id">#{{ idPokemon }}</div>
        <h1 class="nombre" >{{ pokemonName }}</h1>
        <h4 class="tipo">Tipo: 
          <div class="tipos-container">
            <div v-for="type in pokemonTypes" :key="type" class="tipo-cuadro" :style="{ backgroundColor: oscurecerColor(colorSegunTipoDePokemon[type]) }">
              {{ type }}
            </div>
          </div>
        </h4>
      </div>
      <img class="img" :src="imgPokemon" alt="imagen del pokemon"><br>
      <div class="dos">
        <h4>Altura: {{ pokemonAlt }}m</h4><br>
        <h4>Peso: {{ pokemonPeso }}Kg</h4><br>
      </div>
    </div>
    <div v-if="busquedaRealizada" class="atribut">
      <div class="Hp">
        <h5>HP</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(hp)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ hp }}
        </q-circular-progress>
      </div>
      <div class="Attack">
        <h5>ATTACK</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(attack)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ attack }}
        </q-circular-progress>
      </div>
      <div class="SpAttack">
        <h5>SPECIAL ATTACK</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(specialAttack)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ specialAttack }}
        </q-circular-progress>
      </div>
      <div class="Speed">
        <h5>SPEED</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(speed)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ speed }}
        </q-circular-progress>
      </div>
      <div class="Defense">
        <h5>DEFENSE</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(defense)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ defense }}
        </q-circular-progress>
      </div>
      <div class="SpDefence">
        <h5>SPECIAL DEFENSE</h5>
        <q-circular-progress show-value font-size="25px" :value="maximoDeProgresoCircular(specialDefense)" size="100px"
          :thickness="0.22" color="black" track-color="grey-4" class="q-ma-md">
          {{ specialDefense }}
        </q-circular-progress>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";

let name = ref("");
let pokemonName = ref("");
let imgPokemon = ref("");
let idPokemon = ref("");
let pokemonAlt = ref("");
let pokemonPeso = ref("");
let pokemonTypes = ref([]);
let hp = ref("");
let attack = ref("");
let defense = ref("");
let speed = ref("");
let specialAttack = ref("");
let specialDefense = ref("");
let busquedaRealizada = ref(false); // Variable de estado para controlar la visibilidad

function maximoDeProgresoCircular(value) {
  return (value / 255) * 100;
}

async function traer() {
  let res = await axios.get(`https://pokeapi.co/api/v2/pokemon/${name.value.toLowerCase()}`);
  pokemonTypes.value = res.data.types.map(typeInfo => typeInfo.type.name);
  pokemonName.value = res.data.name;
  imgPokemon.value = res.data.sprites.other["official-artwork"].front_default;
  idPokemon.value = res.data.id;
  pokemonAlt.value = res.data.height; // Convertimos decímetros a metros
  pokemonPeso.value = res.data.weight; // Convertimos hectogramos a kilogramos
  hp.value = res.data.stats[0].base_stat;
  attack.value = res.data.stats[1].base_stat;
  defense.value = res.data.stats[2].base_stat;
  specialAttack.value = res.data.stats[3].base_stat;
  specialDefense.value = res.data.stats[4].base_stat;
  speed.value = res.data.stats[5].base_stat;

  cambiarColorSegunTipoDePokemon();
  busquedaRealizada.value = true; // Actualizamos la variable de estado
}

let colorSegunTipoDePokemon = {
  fire: "#FF5733",
  grass: "#4CAF50",
  water: "#2196F3",
  electric: "#FFC107",
  ground: "#795548",
  poison: "#9C27B0",
  rock: "#607D8B",
  bug: "#CDDC39",
  dragon: "#673AB7",
  psychic: "#FF4081",
  ghost: "#607D8B",
  dark: "#212121",
  steel: "#9E9E9E",
  fairy: "#E91E63",
  fighting: "#FF5252",
  normal: "#FFFFFF",
  ice: "#03A9F4",
  flying: "#03A9F4"
};

function cambiarColorSegunTipoDePokemon() {
  let color = colorSegunTipoDePokemon[pokemonTypes.value[0]]; // Usamos el primer tipo para el color
  document.body.style.backgroundColor = color;
}

function oscurecerColor(color, porcentaje = 0.3) {
  // Convertir el color hex a RGB
  let r = parseInt(color.slice(1, 3), 16);
  let g = parseInt(color.slice(3, 5), 16);
  let b = parseInt(color.slice(5, 7), 16);

  // Oscurecer el color
  r = Math.floor(r * (1 - porcentaje));
  g = Math.floor(g * (1 - porcentaje));
  b = Math.floor(b * (1 - porcentaje));

  // Convertir de nuevo a formato hex
  r = r.toString(16).padStart(2, '0');
  g = g.toString(16).padStart(2, '0');
  b = b.toString(16).padStart(2, '0');

  return `#${r}${g}${b}`;
}
</script>

<style>
body {
  /*background-color: #ec2020; */
  background-color:rgba(227, 224, 224, 0.716);
}
.padre {
  justify-content: center;
  text-align: center;
  font-family: 'Acme';
  font-weight: 800;
  /* Peso de la fuente (bold) */
}
.Parte1 {
  display: flex;
  margin-top: 20px;
}

.pokedex {
  width: 17%;
  margin-left: 10%;
}
.buscador{
  width: 20%;
  justify-content: center;
}
.caracteristicas {
  margin-top: 90px;
}

.uno {
  margin-right: 45%;
  margin-top: -3%;
}

.id {
  font-size: 150px;
}

.nombre {
  font-weight: 800;
  /* Peso de la fuente (bold) */
}

.img {
  margin-left: 40%;
  margin-top: -40%;
  width: 30%;
}

.dos {
  margin-left: 60%;
  margin-top: -2%;
  display: flex;
  gap: 3%;
}

.atribut {
  display: grid;
  font-family: 'Acme';
  grid-template-columns: repeat(3, 1fr);
}

.tipos-container {
  display: flex;
  gap: 10px;
  margin-left: 35%;
}

.tipo-cuadro {
  padding: 5px 10px;
  border-radius: 15px;
  color: white;
  font-weight: bold;
}

@media (max-width: 1200px) {

  .nombre {
    font-size: 64px;
  }
  .tipos-container {
  margin-left: 25%;
}
.img {
  margin-left: 50%;
  margin-top: -40%;
  width: 40%;
}
}
@media (max-width: 1000px) {
  .id {
  font-size: 130px;
}
.nombre {
    font-size: 54px;
  }
  .tipos-container {
  margin-left: 25%;
}

.tipo-cuadro {
  padding: 2px 5px;
  font-size: 30px;
}
}
@media (max-width: 600px) {
  .nombre {
    font-size: 36px;
  }
  .id {
    font-size: 100px;
  }
  .img {
    margin-left: 50%;
    margin-top: -40%;
    width: 40%;
  }
  .tipos-container {
    margin-left: 25%;
  }
  .tipo-cuadro {
    padding: 2px 5px;
    font-size: 20px;
  }
  .atribut {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (max-width: 540px) {
  /* Aquí colocas los estilos que se aplicarán cuando el ancho sea menor o igual a 900px */
  .padre {
    /* Por ejemplo, ajustamos el margen y la fuente */
    margin: 10px;
    font-size: 14px;
  }
  .Parte1 {
    /* Ajustamos el diseño de las imágenes */
    display: block;
    text-align: center;
  }
  .gif {
    width: 50%;
    margin-left: 25%;
  }
  .pokedex {
    width: 50%;
    margin-left: 25%;
  }
  .caracteristicas {
    margin-top: 50px;
  }
  .uno {
    margin-right: 0;
    margin-top: 0;
  }
  .id {
    font-size: 100px;
  }
  .nombre {
    font-size: 24px;
  }
  .img {
    margin-left: 0;
    margin-top: 20px;
    width: 80%;
  }
  .dos {
    margin-left: 0;
    margin-top: 20px;
    display: block;
    text-align: center;
  }
  .atribut {
    grid-template-columns: repeat(2, 1fr);
  }
}

</style>
