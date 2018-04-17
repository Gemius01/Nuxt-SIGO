<template>
  <div>
  <v-btn @click="pruebaDivAPdf"> Imprimir PDF</v-btn>
  <div id="reporte">
    <v-card>
    <v-card-title>
        <h1>Mayor Solicitante</h1>
        <v-spacer></v-spacer>
      </v-card-title>
      <column-chart :data="chartData"></column-chart>
        </v-card>
   <div id="caca">
    
  </div>
  </div>
  </div>
</template>
<script>
  import axios from 'axios' // Modulo para realizar las peticiones
  import Vue from 'vue'
  import config from '../config.vue'
  import item from '../components/orden_compra/item.vue'
  import VueChartkick from 'vue-chartkick'
  import Highcharts from 'highcharts'
  import JSPDF from 'jsPDF'
  import html2canvas from 'html2canvas'
  Vue.use(VueChartkick, {adapter: Highcharts})
  export default {
    components: { config, item },
    data: () => ({
      chartData: []
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
        axios.get(config.API_LOCATION + `/bodega/detalle_salida/mayorsolicitante`) // petición GET a Marca para traer a todos los objetos "Marca"
          .then((response) => {
            this.chartData = response.data
          })
          .catch(e => {
          })
      },
      pruebaDivAPdf () {
        html2canvas(document.getElementById('reporte')).then(function (canvas) {
          var doc = new JSPDF()
          var imgData = canvas.toDataURL('image/png')
          doc.addImage(imgData, 'PNG', 0, 0, 150, 100)
          doc.save('sample-file.pdf')
        })
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