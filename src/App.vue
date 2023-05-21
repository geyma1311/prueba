<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <a href="/">
          <h1 class="white--text">PREMIUM VIDEO</h1>
        </a>
      </div>

      <v-spacer></v-spacer>

      <router-link :to="total === 0 ? '#' : '/cart'">
        <v-btn icon class="mr-2 mt-2">
          <v-badge color="green" :content="total.toString()">
            <v-icon>mdi-cart-outline</v-icon>
          </v-badge>
        </v-btn>
      </router-link>
    </v-app-bar>

    <v-main>
      <router-view @updateCart="updateCart" />
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    cart: {
      rent: [],
      sell: [],
    },
    total: 0
  }),
  mounted() {
    if (localStorage.cart) {
      this.cart = JSON.parse(localStorage.cart)
      this.updateCart(this.cart)
    }
  },
  methods: {
    // se actualiza el carrito
    updateCart(cart) {
      this.cart = cart;
      localStorage.cart = JSON.stringify(cart)
      let rents = this.cart.sell.reduce(function (accumulator, curValue) {
        return accumulator + parseInt(curValue.quantity);
      }, 0);
      let sales = this.cart.rent.reduce(function (accumulator, curValue) {
        return accumulator + parseInt(curValue.quantity);
      }, 0);
      // const cant = this.cart.sell.length + this.cart.rent.length
      this.total = sales + rents
    },
  },
};
</script>
<style lang="scss">
  .content {
    width: 60%;
    margin: 0 auto;
  }
</style>