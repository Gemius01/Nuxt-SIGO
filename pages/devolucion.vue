<template>
  <div>
   
    <!-- Dialog Detalle Prestamo -->
      <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>
          <v-card-title><h1> Detalle de Salida</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>RUT Solicitante</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rutSolicitante }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Usuario Responsable</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.usuarioResponsable }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Hora</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Motivo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.motivo }}</v-list-tile-title>
            </v-list-tile>
            <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content v-for="(item,i) in this.itemPrestado" :key="i">
        <div slot="header">Item : {{ item.id_item.nombre }}</div>
        <v-card>
          <v-card-text class="light-green lighten-3">Cantidad : {{ item.cantidad }}</v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
          </v-list>
   
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Detalle Prestamo -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>HISTORIAL DEVOLUCIONES</h1>
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
      :headers="headers"
      :items="items"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
   <!-- Fin Tabla--> 
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.rutSolicitante }}</td>
        <td class="text-xs-center">{{ props.item.usuarioResponsable }}</td>
        <td class="text-xs-center">{{ props.item.hora }}</td>
        <td class="text-xs-center">{{ props.item.fecha }}</td>
        <td class="justify-center layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
            <v-icon color="blue">search</v-icon>
          </v-btn>
          <span>Detalle</span>
          </v-tooltip>
        </td>
      </template>

      <template slot="pageText" slot-scope="{ pageStart, pageStop }">
        De {{ pageStart }} a {{ pageStop }}
      </template>
    </v-data-table>
    </v-card>
  </div>
</template>
<script>
  import axios from 'axios' // Modulo para realizar las peticiones
  import config from '../config.vue'
  import item from '../components/prestamo/item.vue'
  export default {
    components: { config, item },
    data: () => ({
      textoRules: config.rules,
      errorMessages: [],
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Subcategoría
      dialogDevolver: false, // prop para abrir y cerrar modal de Editar Subcategoría
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Subcategoría
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Subcategoría
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      devolverIndex: -1,
      labelCantidad: 'Disponible : ' + 0 + '',
      categoriaSelectID: {},
      categoriaSelectIDEdit: {},
      value: '',
      selectItem: 0,
      search: '',
      inputArray: [{idInput: 'selectItemPrestamo', idCantidad: 'cantidad'}],
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      categorias: [],
      headers: [ // Encabezados de  la tabla
        { text: 'Solicitante', value: 'rutSolicitante', width: '20%', align: 'center' },
        { text: 'Responsable', value: 'usuarioResponsable', width: '20%', align: 'center' },
        { text: 'Hora', value: 'hora', width: '20%', align: 'center' },
        { text: 'Fecha', value: 'fecha', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      items: [],
      valid: true,
      cantidadInsumos: null,
      addItem: {
        id: 0,
        solicitante: '',
        motivo: '',
        cantidad: 0,
        itemID: '',
        stock: 0
      },
      devolverItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        nombre: '',
        categoria: '',
        id_categoria: 0
      },
      detailItem: {
        id: 0,
        rutSolicitante: '',
        usuarioResponsable: '',
        hora: null,
        fecha: ''
      },
      itemPrestado: [],
      deleteItem: {
        id: 0,
        nombre: '',
        categoria: '',
        id_categoria: 0
      },
      defaultItem: {
        name: '',
        calories: 0,
        fat: 0,
        carbs: 0,
        protein: 0
      }
    }),
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      }
    },
    watch: {
      dialog (val) {
        val || this.close()
      },
      selectItemPrestamo (val) {
        this.addItem.stock = val.stock
        this.cambioLabel1(val.stock)
      }
    },
    created () {
      this.initialize()
    },
    methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/prestamo/valueTrue`) // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      close () {
        this.dialog = false
      },
      clearAddModal () {
        this.$refs.fAgregarSubCat.reset()
        this.dialogAdd = false
      },
      clearEditModal () {
        this.dialogEdit = false
      },
      modalDetalle (item) {
        var id = item.id
        axios.get(config.API_LOCATION + '/bodega/detalle_prestamo/' + id + '/prestamo') // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.itemPrestado = response.data
            this.detailItem = this.items.indexOf(item) // obtener posición del array
            this.detailItem = Object.assign({}, item)
            console.log(this.detailItem)
            this.dialogDetail = true
          })
          .catch(e => {
          })
      },
      cerrarModalDetail () {
        this.dialogDetail = false
      }
    }
  }
</script>