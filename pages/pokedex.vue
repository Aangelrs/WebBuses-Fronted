<template>
  <v-app>
    <!--<h1>Pokedex</h1>-->
    <PokemonLogo/>
    <v-container>
      <v-card color="menu">
        <v-container>
          <!--{{pokemons}}  Para mostrar todo los datos del json medainte el arreglo pokemons -->

          <!--Buscador de pokemon llamando search-->
          <v-text-field
            v-model="search"
            label="Buscar Pokemon"
            placeholder="Pikachu"
            prepend-icon="mdi-magnify"
            clearable
          ></v-text-field>
          <v-row>
            <v-col
              v-for="pokemon in filteredPokemons"
              :key="pokemon.name"
              cols="2"
            >
              <!--v-for="pokemon in pokemons" :key="pokemon.name"-->
              <v-hover 
                    v-slot="{ hover }" 
                    close-delay="200">
                <!--Al hacer click en el v-card llama el dialogo showDescriptionPokemon-->
                <v-card
                  color="menu2"
                  :elevation="hover ? 12 : 2"
                  @click="showPokemon(get_id(pokemon))"
                >
                  {{ get_id(pokemon) }}

                  <v-container>
                    <v-row  class="mx-0 d-flex justify-center">
                      <img
                        :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/home/${get_id(
                          pokemon
                        )}.png`"
                        :alt="pokemon.name"
                        width="80%"
                      
                      />
                    </v-row>
                    <v-row justify="center">
                      <h2 class="text-center">{{ getName(pokemon) }}</h2>
                    </v-row>
                  </v-container>
                </v-card>
              </v-hover>
            </v-col>
          </v-row>
        </v-container>
      </v-card>
    </v-container>
    <!--Ventana para mostrar la description del pokemon-->
    <v-dialog v-model="showDescriptionPokemon" width="700">
      <v-card v-if="selectPokemon" >
        <template v-slot:title >
          <div class="text-h4 my-1 text-center">
            {{ getName(selectPokemon) }}
          </div>
        </template>

        <v-container>
          <v-row>
            <v-col cols="4">
              <v-card justify="center" :image="backgroundPoke">
               
                  <img
                    :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/home/${selectPokemon.id}.png`"
                    :alt="selectPokemon.name"
                    width="80%"
                    align="center"
                    justify="center"
                  />
                
              </v-card>
            </v-col>
            <v-col cols="8">
              <v-chip>
                Altura: {{ (selectPokemon.height / 10).toFixed(1) }} m
              </v-chip>
              <v-chip class="ml-2">
                Peso: {{ (selectPokemon.weight / 10).toFixed(1) }} kg
              </v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip
                v-for="typePoke in selectPokemon.types"
                :key="typePoke.slot"
                label
                class="mr-2"
                :color="getColorType(typePoke.type.name)"
              >
                <!--Mostrar nombre es typePoke.type.name-->
                {{ getName(typePoke.type) }}
              </v-chip>
            </v-col>
            <v-col cols="30" class="justify-center">
              <v-chip
                v-for="stats in selectPokemon.stats"
                :key="stats.stat"
                class="mr-2"
              >
                {{ getName(stats.stat) + ": " + stats.base_stat }}
              </v-chip>
            </v-col>
          </v-row>
          <v-container>
            <h2>Habilidades</h2>

            <v-expansion-panels>
              <v-expansion-panel>
                <v-expansion-panel-title> Movimientos </v-expansion-panel-title>
                <v-expansion-panel-text>
                  <v-table>
                    <thead>
                      <tr>
                        <th class="text-left">Nivel</th>
                        <th class="text-left">Nombre</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr
                        v-for="item in filterMoves(selectPokemon)"
                        :key="item.move.name"
                      >
                        <td>{{ getLevelMove(item) }}</td>
                        <!--item.move.name-->
                        <td>{{ getName(item.move) }}</td>
                      </tr>
                    </tbody>
                  </v-table>
                </v-expansion-panel-text>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-container>
          {{ selectPokemon.id }} ID
          {{ selectPokemon.weight }}
        </v-container>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="showDescriptionPokemon = false">
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Descripcion dialogo ventana-->
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  name: "AppPokedex",
  comments: {},

  data() {
    return {
      pokemons: [],
      search: "",
      showDescriptionPokemon: false,
      selectPokemon: null,
      backgroundPoke: "",
    };
  },

  computed: {
    filteredPokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      });
    },
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=100")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },

  methods: {
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6]);
    },
    getName(pokemon) {
      return pokemon.name[0].toUpperCase() + pokemon.name.slice(1);
    },
    showPokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selectPokemon = response.data;
        console.log(response.data);
        this.showDescriptionPokemon = !this.showDescriptionPokemon;
      });
    },
    filterMoves(pokemon) {
      // eslint-disable-next-line array-callback-return
      return pokemon.moves.filter((item) => {
        let include = false;
        for (const version of item.version_group_details) {
          if (
            version.version_group.name === "sword-shield" &&
            version.move_learn_method.name === "level-up"
          ) {
            include = true;
          }
        }
        return include;
      });
    },
    getLevelMove(move) {
        let movimientos = {};
      for (const version of move.version_group_details) {
        if (
          version.version_group.name === "sword-shield" &&
          version.move_learn_method.name === "level-up"
        ) {
          return version.level_learned_at;
        }
      }
      return 0;
    },
    getColorType(type) {
      console.log(type);
      switch (type) {
        case "fire":
          this.backgroundPoke = "background.png";
          return "red";
        case "water":
          this.backgroundPoke = "background.png";
          return "blue darken-1";
        case "grass":
          this.backgroundPoke = "background.png";
          return "green darkeen-1";
        case "poison":
          this.backgroundPoke = "background.png";
          return "deep-purple lighten-2";
        case "flying":
          this.backgroundPoke = "backAire.png";
          return "blue lighten-3";
        case "bug":
          this.backgroundPoke = "background.png";
          return "lime darken-1";
        case "electric":
          this.backgroundPoke = "background.png";
          return "yellow darken-1";
        case "ground":
          this.backgroundPoke = "backTierra.png";
          return "brown darken-2";
        case "fairy":
          return "pink lighten-3";
        case "psychic":
          this.backgroundPoke = "backpsi.png";
          return "pink darken-1";
        case "fighting":
          this.backgroundPoke = "backfight.png";
          return "orange darken-3";
        case "dragon":
          return "purple darken-3";
        case "rock":
          this.backgroundPoke = "backTierra.png";
          return "brown lighten-3";
        case "ice":
          this.backgroundPoke = "backIce.png";
          return "cyan accent-1";
        case "dark":
          return "grey darken-4";
        case "steel":
          return "grey darken-3";
        case "ghost":
          this.backgroundPoke = "backNight.png";
          return "indigo darken-1";
        default:
          return "grey lighten-1";
      }
    },
    test(test) {
      console.log(test);
    },
  },
};
</script>
