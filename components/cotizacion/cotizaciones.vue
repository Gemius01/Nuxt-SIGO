<template>
<div>
  <!-- Textfields -->
  <div v-for="item in inputItem" :name="item.idInput" class="divItem">
  <h4 class="textDiv">Cotizaciones</h4>
  <!--
  <v-btn color="error" class="deleteBtn" @click="eliminarItem">
        <v-icon dark>clear</v-icon>
      </v-btn>
    -->
  <v-flex xs12 >
  <v-container grid-list-md text-xs-center>
  <v-layout row wrap>
  <v-flex xs12 class="cotizacionIncompleta">
    <v-container grid-list-md text-xs-center>
  <v-layout row wrap>
    <v-flex xs2>
        <v-text-field  label="N°" value="1"  disabled></v-text-field>
    </v-flex>
    <v-flex xs4>
     <v-text-field  value="Coca Cola" label="Proveedor"  disabled></v-text-field>
    </v-flex>
    <v-flex xs4 >
     <v-text-field  label="Total" value="$10.000"  disabled></v-text-field> 
    </v-flex>
    <v-flex xs1>
      <v-btn color="blue" class="btnOptions">
        <v-icon dark>search</v-icon>
      </v-btn>
    </v-flex>
    <v-flex xs1>
       <v-checkbox
        v-model="checkbox"
      ></v-checkbox>
    </v-flex>
  </v-layout>
</v-container>
        </v-flex>
        </v-layout>
        </v-container>
    
    </v-flex>  
    </div>
    <!-- Fin Textfields -->
 

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
    count: 1,
    checkbox: false
  }),
  methods: {
    change (e) {
      this.selectedItem = e
    },
    agregarItemPrestado () {
      this.inputItem.push({idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', idValor: 'idValor' + this.count + '', selectedItem: 0})
      this.$emit('clicked', {idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', idValor: 'idValor' + this.count + ''})
      this.count = this.count + 1
    },
    cargarSelectCat () {
      axios.get(config.API_LOCATION + '/bodega/item/') // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
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
    this.inputItem.push({idInput: 'selectItemPrestamo' + this.count + '', idCantidad: 'cantidad' + this.count + '', idValor: 'idValor' + this.count + '', selectedItem: 0})
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
    width:20%;
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

  .btnOptions{
    min-width: 0%;
    height: 30%;
    text-align: right;
    width:90%;
  }

</style>