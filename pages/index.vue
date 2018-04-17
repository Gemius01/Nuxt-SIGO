<template>
  <v-layout column>
    <h3 class="display-2" v-text="bienvenida">Bienvenidos</h3>
  </v-layout>
</template>

<script>

export default {
  data () {
    return {
      bienvenida: 'Bienvenidos'
    }
  }
}
</script>
