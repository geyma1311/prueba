<template>
  <div class="content">
    <AlertComponent
      v-if="alertActive"
      @closeAlert="alertActive = false"
      :alert="alert"
    />
    <v-col cols="12" sm="6">
      <v-text-field
        v-model="search"
        solo
        label="Buscar pelicula"
        @change="getMovies"
      ></v-text-field>
    </v-col>
    <div class="d-flex flex-wrap justify-space-around">
      <div class="d-flex flex-wrap justify-space-around" v-if="loading">
        <v-sheet
          v-for="(movie, index) in 10"
          :key="index"
          :color="`grey lighten-4`"
          class="pa-3"
        >
          <v-skeleton-loader
            class="mx-auto"
            width="300"
            type="card"
          ></v-skeleton-loader>
        </v-sheet>
      </div>

      <div v-else class="pa-2" v-for="(movie, index) in movies" :key="index">
        <MovieComponent :movie="movie" v-once>
          <template v-slot:actions>
            <v-btn color="orange" @click="openModal('rent',movie)" text> Rentar </v-btn>
            <v-btn color="orange" @click="openModal('sell',movie)" text> Comprar </v-btn>
          </template>
        </MovieComponent>
      </div>

      <v-dialog v-model="dialogRent" width="500">
        <v-card>
          <v-card-title class="text-h5 primary white--text">
            Rentar pelicula {{movie ? movie.nombre : ''}}
          </v-card-title>
          <v-card-text class="pa-0">
            <div class="d-flex pa-0 pt-2">
              <v-col cols="12" md="4">
                <v-text-field
                  :elevation="0"
                  v-model="quantity"
                  label="cantidad"
                  required
                  outline
                  type="number"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-menu
                  ref="menu"
                  v-model="menu"
                  :close-on-content-click="false"
                  :return-value.sync="date"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="date"
                      label="Fecha"
                      readonly
                      v-bind="attrs"
                      outline
                      v-on="on"
                      clearable
                    ></v-text-field>
                  </template>
                  <v-date-picker v-model="date" no-title scrollable>
                    <v-spacer></v-spacer>
                    <v-btn text color="primary" @click="menu = false">
                      Cancel
                    </v-btn>
                    <v-btn text color="primary" @click="$refs.menu.save(date)">
                      OK
                    </v-btn>
                  </v-date-picker>
                </v-menu>
              </v-col>
            </div>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" outlined @click="movie = null; dialogRent = false">
              Cancelar
            </v-btn>
            <v-btn color="primary" :disabled="!date" @click="rent(); dialogRent = false">
              Guardar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="dialogSell" width="500">
        <v-card>
          <v-card-title class="text-h5 primary white--text">
            Comprar pelicula
          </v-card-title>
          <v-card-text class="pa-0">
            <div class="d-flex pa-0 pt-2">
              <v-col cols="12" md="12">
                <v-text-field
                  :elevation="0"
                  v-model="quantity"
                  label="cantidad"
                  required
                  outline
                  type="number"
                ></v-text-field>
              </v-col>
            </div>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" outlined @click="movie = null; dialogSell = false">
              Cancelar
            </v-btn>
            <v-btn color="primary" @click="sell(); dialogSell = false">
              guardar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import MovieComponent from "@/components/MovieComponent.vue";
import AlertComponent from "@/components/AlertComponent.vue";
export default {
  name: "Home-view",
  components: {
    AlertComponent,
    MovieComponent,
  },
  data() {
    return {
      movies: [],
      search: null,
      alert: {
        text: null,
        type: null,
      },
      loading: false,
      cart: {
        rent: [],
        sell: [],
      },
      alertActive: false,
      dialogRent: false,
      dialogSell: false,
      quantity: 1,
      date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      menu: false,
      movie: null,
      action: 'sell'
    };
  },
  created() {
    this.getMovies();
  },
  mounted() {
    if (localStorage.cart) {
      this.cart = JSON.parse(localStorage.cart)
    }
  },
  methods: {
    getMovies() {
      this.loading = true;

      this.axios
        .get(
          `https://www.omdbapi.com/?apikey=5eec5adc&s=${
            this.search ? this.search : "null"
          }`
        )
        .then(({ data }) => {
          if (data.Error) {
            throw data.Error;
          }
          this.movies = data.Search;
          this.alertActive = false;
        })
        .catch(() => {
          // this.movies = [];
          this.alertActive = true;
          this.alert.type = "error";
          this.alert.text = "Error al obtener listado. Intente nuevamente";
        })
        .finally(() => {
          this.loading = false;
        });
    },
    // funcion activado por el evento en el componente de movieComponent
    openModal (action, movie) {
      action === 'sell' ? this.dialogSell = true :  this.dialogRent = true;
      this.movie = {...movie}
      this.action = action
    },
    // funcion para rentar pelicula
    rent() {
      if (!this.cart.rent.map((el) => el.imdbID).includes(this.movie.imdbID)) {
        this.movie.quantity = this.quantity
        this.movie.fecha = this.date
        this.cart.rent.push(this.movie);
        this.$emit("updateCart", this.cart);
        this.resetDatos()
      } else {
        this.cart.rent.find(el => el.imdbID === this.movie.imdbID).quantity += this.quantity
        this.$emit("updateCart", this.cart);
        this.resetDatos()
      }
    },
    // funcion para venta pelicula
    sell() {
      if (!this.cart.sell.map((el) => el.imdbID).includes(this.movie.imdbID)) {
        this.movie.quantity = this.quantity
        this.cart.sell.push(this.movie);
        this.$emit("updateCart", this.cart);
        this.resetDatos()
      } else {
        this.cart.sell.find(el => el.imdbID === this.movie.imdbID).quantity += this.quantity
        this.$emit("updateCart", this.cart);
        this.resetDatos()
      }
    },
    // reinicio lo datos despues de rentar o comprar
    resetDatos () {
      this.quantity = 1
      this.movie = null
      this.dialogRent = false
      this.dialogSell = false
    }
  },
};
</script>