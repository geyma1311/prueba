<template>
<div class="alerta-main">
  <v-alert v-if="alert.text" class="alerta" :type="alert.type">
    {{ alert.text }}
    <v-btn class="primary ml-10" @click="closeAlert">Aceptar</v-btn>
  </v-alert>
</div>
  
</template>

<script>

  export default {
    name: 'AlertComponent',
    props: {
      alert: Object
    },
    data: () => ({
      msg: null,
    }),
    watch: {
      alert: {
        handler() {          
          this.msg = this.alert.text
          // clearInterval()
          // setTimeout(() => {
          //   this.msg = null
          // }, 4000);
        },
        deep: true
      },
    },
    methods: {
      closeAlert () {
        this.$emit('closeAlert')
      }
    }
  }
</script>
<style scoped>
.alerta-main {
  position: fixed;
  width: inherit;
  justify-content: center;
  display: flex;
  z-index: 9;
}
.alerta {
  position: relative;
  width: max-content;
  z-index: 2;
  top: 5px;
}
</style>