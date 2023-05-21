<template>
  <div class="alerta-main">
    <v-card class="mx-auto" :width="width">
      <v-img
        class="white--text align-end"
        height="200px"
        width="300px"
        :src="movie.Poster"
      >
      </v-img>
      <v-card-title>{{ movie.Title }}</v-card-title>
      <v-card-subtitle class="pb-0">
        {{ movie.Year }}
      </v-card-subtitle>

      <v-card-text class="text--primary pb-0">
        <div>{{ movie.Type }}</div>
        <slot></slot>
      </v-card-text>
      

      <v-card-actions>
        <slot name="actions"></slot>
        <!-- <v-btn color="orange" @click="rent()" text> Rentar </v-btn>
        <v-btn color="orange" @click="sell()" text> Comprar </v-btn> -->
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
export default {
  name: "MovieComponent",
  props: {
    movie: Object,
    width: {
      type: String,
      default: '300'
    }
  },
  data: () => ({}),
  methods: {
    // rent () {
    //   this.$emit('openModal', {action: 'rent', 'movie': {...this.movie}})
    // },
    // sell () {
    //   this.$emit('openModal', {action: 'sell', 'movie': {...this.movie}})
    // }
  },
};
</script>