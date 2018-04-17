<template>
<div>
  <!-- Textfields -->
  <div v-for="item in inputItem" :name="item.idInput" class="divItem">
  <h4 class="textDiv">Item</h4>
  <v-btn color="error" class="deleteBtn" @click="eliminarItem">
        <v-icon dark>clear</v-icon>
      </v-btn>
  <v-flex xs12 sm12 md12>
    <v-flex xs12>
      <v-select
         :id="item.idInput"
         :items="arrayItems"
         item-text="nombre"
         item-value="id"
         :value="item.selectItemPrestado"
         v-model="item.selectItemPrestado"
         @change='change' 
         required
         autocomplete
         label="Item"
         single-line
      ></v-select>
    </v-flex>
    <v-flex xs12 >
    <v-text-field :id="item.idCantidad" label="Cantidad" required></v-text-field>
    </v-flex>  
    </v-flex>   
    </div>
    <!-- Fin Textfields -->
    <!-- Botón Agregar -->
    <v-flex xs12 >
      <v-btn block dark color="primary" @click="agregarItemPrestado">
        AGREGAR ITEM
      </v-btn>
    </v-flex>
    <!-- fin Botón Agregar -->
  </v-flex>

</div>
</template>
<script>
import axios from 'axios' // Modulo para realizar las peticiones
import config from '../../config.vue'
export default {
  components: { config },
  data: () => ({
    inputItem: [],
    arrayItems: [],
    count: 1
  }),
  methods: {
    change (e) {
      this.selectedItem = e
    },
    agregarItemPrestado () {
      this.inputItem.push({idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', selectItemPrestado: 0})
      this.$emit('clicked', {idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + ''})
      this.count = this.count + 1
    },
    cargarSelectCat () {
      axios.get(config.API_LOCATION + '/bodega/item/herramienta') // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
        .then((response) => {
          console.log(response.data)
          this.arrayItems = response.data
        })
        .catch(e => {
        })
    },
    eliminarItem (e) {
      console.log(e.path)
      var nameDiv = e.path[2].getAttribute('name')
      this.$emit('deleted', nameDiv)
      e.path[1].parentNode.remove()
    }
  },
  created () {
    this.cargarSelectCat()
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
    width:10%;
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