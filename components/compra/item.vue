<template>
<div>
  <!--  Textfields -->
  <v-btn @click="pruebaClick"></v-btn>
  <div v-for="(item, key, index) in this.prueba"  class="divItem">
  <h4 class="textDiv">Item</h4>
  <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs4 style="display:none;">
    <v-text-field   label="Nombre"   disabled></v-text-field>
  </v-flex>
  <v-flex xs4>
    <v-text-field  label="Nombre"  disabled></v-text-field>
  </v-flex>
  <v-flex xs4>
    <v-text-field   label="Cantidad"  disabled></v-text-field>
  </v-flex>
   <v-flex xs4>
    <v-text-field  label="Valor Unit" type="number" required></v-text-field>
    </v-flex>
    </v-layout>
    </v-container> 
    </div>
    <!-- Fin Textfields -->
  </v-flex>
</div>
</template>
<script>
import axios from 'axios' // Modulo para realizar las peticiones
import config from '../../config.vue'
export default {
  components: { config },
  props: ['ordenCompraSelected', 'prueba'],
  data: () => ({
    inputItem: [], // Tiene un objeto adentro de prueba
    arrayItems: [],
    count: 1
  }),
  watch: {
    prueba (e) {
      console.log(e)
    }
  },
  methods: {
    pruebaClick () {
      console.log(this.prueba)
    },
    change (e) {
      this.selectedItem = e
    },
    agregarItemPrestado () {
      this.inputItem.push({idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', idValor: 'idValor' + this.count + '', selectedItem: 0})
      this.$emit('clicked', {idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', idValor: 'idValor' + this.count + ''})
      this.count = this.count + 1
    },
    cargarItems (e) {
      axios.get(config.API_LOCATION + '/bodega/detalle_adiquisicion/' + this.prueba + '/orden') // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
        .then((response) => {
          this.inputItem = [1]
        })
        .catch(e => {
        })
    },
    ordenCompraChange (e) {
      console.log(e)
    },
    eliminarItem (e) {
      console.log(e.path)
      var nameDiv = e.path[2].getAttribute('name')
      this.$emit('deleted', nameDiv)
      e.path[1].parentNode.remove()
    }
  },
  created () {
    // this.cargarItems()
  }
}
</script>
<style>
  div .divItem{
    border-radius: 5px;
    border-style: solid;
    border-width: 4px;
    border-color: grey;
  }
  .textDiv{
    text-align: center;
    width:15%;
    margin-top:-3%;
    margin-left:4%;
    background:white;
  }
  .deleteBtn{
    min-width: 0%;
    min-height: 0%;
    margin-top:-7%;
    margin-bottom:-4%;

    margin-left:94%;
    height: 6%;
    text-align: right;
    width:6%;
  }
</style>