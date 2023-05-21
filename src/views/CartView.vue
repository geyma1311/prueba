<template>
  <div class="my-5 content">
      <AlertComponent
        v-if="alertActive"
        @closeAlert="alertActive = false"
        :alert="alert"
      />
      <h2>Para rentar</h2>
      <div class="d-flex">
        <div class="pa-2 d-flex" v-for="(movie) in cart.rent" :key="movie.imdbID">
          <MovieComponent :movie="movie" v-once>
            <div class="d-flex align-center justify-space-between">
              <span class="">Cantidad: </span>
              <div cols="12" style="width: 80px">
                <v-text-field
                  class="pa-0"
                  :elevation="0"
                  required
                  :value="movie.quantity"
                  hide-details
                  outline
                  type="number"
                  @change="updateCart($event, movie.imdbID, 'rent')"
                ></v-text-field>
              </div>
              <!-- <span class="pr-2">x {{movie.quantity}} </span> -->
            </div>
            <div class="d-flex align-center justify-space-between">
              <span class="">Fecha: </span>
              <div cols="12" style="width: 80px">
                <v-text-field
                  class="pa-0"
                  :elevation="0"
                  required
                  readonly
                  :value="movie.fecha"
                  hide-details
                  outline
                ></v-text-field>
              </div>
              <!-- <span class="pr-2">x {{movie.quantity}} </span> -->
            </div>
            <div class="d-flex pt-4 justify-start">
              <v-btn class="error" block small @click="deleteItem(movie.imdbID, 'rent')">
                Eliminar
                <v-icon >
                  mdi-delete
                </v-icon>
              </v-btn>
            </div>
          </MovieComponent>
        </div>
      </div>
      <v-divider class="my-8"></v-divider>
      <h2>Para comprar</h2>
      <div class="d-flex">
        <div class="pa-4 d-flex" v-for="(movie) in cart.sell" :key="movie.imdbID">
          <MovieComponent :movie="movie" v-once>
            <div class="d-flex justify-space-between">
              <span class="">Cantidad: </span>
              <div cols="12" style="width: 80px">
                <v-text-field
                  class="pa-0"
                  :elevation="0"
                  required
                  :value="movie.quantity"
                  hide-details
                  outline
                  type="number"
                  @change="updateCart($event, movie.imdbID, 'sell')"
                ></v-text-field>
              </div>
            </div>
            <div class="d-flex pt-4 justify-start">
              <v-btn class="error" block small @click="deleteItem(movie.imdbID, 'sell')">
                Eliminar
                <v-icon >
                  mdi-delete
                </v-icon>
              </v-btn>
            </div>
          </MovieComponent>
        </div>
      </div>
      <div class="">
        <v-btn class="success" @click="buy()" block>
          Finalizar
        </v-btn>
      </div>
  </div>
</template>

<script>
import MovieComponent from '@/components/MovieComponent.vue'
import AlertComponent from "@/components/AlertComponent.vue";
  export default {
    components: {
      AlertComponent,
      MovieComponent,
    },
    name: 'Cart-View',
    data: () => ({
      cart: {
        rent: [],
        sell: [],
      },
      alertActive: false,
      alert: {
        text: null,
        type: null,
      },
    }),
    mounted() {
      if (localStorage.cart) {
        this.cart = JSON.parse(localStorage.cart) 
      }
    },
    methods: {
      deleteItem (id, action) {
        this.cart[action === 'sell' ? 'sell' : 'rent'] = this.cart[action === 'sell' ? 'sell' : 'rent'].filter(el => el.imdbID !== id)
        localStorage.cart = JSON.stringify(this.cart)
      },
      buy () {
        this.alert.text = 'Su compra fue exitosa'
        this.alert.type = "success";
        this.alertActive = true
        this.cart = this.$options.data().cart
        this.$emit("updateCart", this.cart);
        // this.$router.push('/')
      },
      updateCart (quantity, id, action) {
        this.cart[action === 'sell' ? 'sell' : 'rent'].find(el => el.imdbID === id).quantity = quantity
        this.$emit("updateCart", this.cart);
        // localStorage.cart = JSON.stringify(this.cart)

      }
    }
  }
</script>
