<template>
  <div>
  <v-btn @click="pruebaDivAPdf"> Imprimir PDF</v-btn>
  <div id="reporte">
     <v-card>
    <v-card-title>
        <h1>Reporte Herramienta Crítico</h1>
        <v-spacer></v-spacer>
        <v-text-field
          append-icon="search"
          label="Buscar"
          single-line
          hide-details
          v-model="search"
        ></v-text-field>
      </v-card-title>
    <v-data-table
      id="tabla"
      :headers="headers"
      :items="chartData"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
   <!-- Fin Tabla-->
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.nombre }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.id_marca.nombre }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.modelo }}</td>
        <td class="text-xs-center">{{ props.item.stock }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.stockCritico }}</td>
        <td class="text-xs-center">{{ props.item.id_unidad_medida.medida }}</td>
        <td class="text-xs-center">{{ props.item.id_subcategoria.nombre }}</td>
        <td class="justify-center layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
            <v-icon color="blue">search</v-icon>
          </v-btn>
          <span>Detalle</span>
          </v-tooltip>
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalEdit(props.item)" >
            <v-icon color="green">edit</v-icon>
          </v-btn>
          <span>Editar</span>
          </v-tooltip>
          <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDelete(props.item)">
            <v-icon color="red">delete</v-icon>
          </v-btn>
          <span>Eliminar</span>
          </v-tooltip>
        </td>
      </template>

      <template slot="pageText" slot-scope="{ pageStart, pageStop }">
        De {{ pageStart }} a {{ pageStop }}
      </template>
    </v-data-table>
    </v-card>
    <table id="tabla2">
      
    </table>
  </div>
  </div>
</template>
<script>
  import axios from 'axios' // Modulo para realizar las peticiones
  import config from '../config.vue'
  import item from '../components/orden_compra/item.vue'
  // import VueChartkick from 'vue-chartkick'
  // import Highcharts from 'highcharts'
  // var JSPDF = require('jspdf')
  // require('jspdf-autotable')
  // import html2canvas from 'html2canvas'
  export default {
    components: { config, item },
    data: () => ({
      chartData: [],
      pagination: {}, // paginación de la tabla
      search: '',
      headers: [ // Encabezados de  la tabla
        { text: 'ID', value: 'id', sortable: true, width: '20%', align: 'center' },
        { text: 'Nombre', value: 'nombre', width: '20%', align: 'center' },
        { text: 'Stock', value: 'stock', width: '20%', align: 'center' },
        { text: 'Unidad de Medida', value: 'unidadmedida', width: '20%', align: 'center' },
        { text: 'Subcategoria', value: 'subcategoria', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ]
    }),
    computed: {
    },
    watch: {
    },
    created () {
      this.datosReporte()
    },
    methods: {
      datosReporte () {
        axios.get(config.API_LOCATION + '/bodega/item/reportHerramientaCritic') // petición GET a Marca para traer a todos los objetos "Marca"
          .then((response) => {
            this.chartData = response.data
          })
          .catch(e => {
          })
      },
      pruebaDivAPdf () {
      }
    }
  }
</script>

<style>
  div .divItemCompra{
    border-radius: 5px;
    border-style: solid;
    border-width: 4px;
    border-color: grey;
  }
  .textDivCotizacion{
    text-align: center;
    width:19%;
    margin-top:-4%;
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
  .cotizacionIncompleta{
    background-color:red;
    border-radius: 4px;
    margin-bottom: 0%;
  }
  .cotizacionCompleta{
    background-color:rgba(24, 243, 21, 0.70);
    border-radius: 4px;
    margin-bottom: 0%;
  }
</style>