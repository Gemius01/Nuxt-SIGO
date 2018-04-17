<template>
  <div>
  <!-- SnackBar (mensaje de Success)-->
 <v-snackbar
      :timeout="timeout"
      :color="color"
      :multi-line="mode === 'multi-line'"
      :vertical="mode === 'vertical'"
      v-model="snackbar"
    >
      {{ text }}
      <v-btn dark flat @click.native="snackbar = false">Cerrar</v-btn>
    </v-snackbar>
    <!-- Fin de SnackBar-->
  <!-- Dialog  Realizar Prestamo -->
    <v-dialog v-model="dialogAdd"  max-width="500px" >
            <v-form @submit.prevent="agregarPrestamo" v-model="valid" ref="fAgregarSubCat" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Realizar Prestamo</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap >
              <v-flex xs12 sm12 md12>
                <v-text-field label="RUT Solicitante"  :rules="textoRules" :counter="20" name="solicitante" v-model="addItem.solicitante" required></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Motivo"  :rules="textoRules" :counter="20" name="motivo" v-model="addItem.motivo" required></v-text-field>
              </v-flex>
              <v-flex xs6>
          <v-select
            id="selectItemPrestamo"
            :items="categorias"
            item-text="nombre"
            :value="selectItem.id"
            v-model="selectItem"
            @change="changeSelectPrestamo"
            search-input
            :rules="[() => !!this.selectItem || 'Campo requerido']"
            :error-messages="errorMessages"
            required
            autocomplete
            label="Item"
            single-line
          ></v-select>
        </v-flex>
        <v-flex xs6>
        <v-text-field v-bind:label="labelCantidad" v-on:keyup="cambioLabel2" @click="cambioLabel2" :rules="[(v) => (v) <= parseInt(this.cantidadInsumos) || 'Cantidad supera',(v) => (v) > 0 || 'Es Menor a 0']"  type="number" id="cantidad" v-model="addItem.cantidad" required></v-text-field> 
        </v-flex>
        <v-flex xs12 id="divItems">
        <item x12 @clicked="onClickChild" @deleted="itemEliminado"></item>
        </v-flex>
        
      
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions >
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" outline flat @click="clearAddModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" outline type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Realizar Prestamo -->
    <!-- Dialog Devolver Prestamo -->
       <v-dialog v-model="dialogDevolver" max-width="500px">
        <form @submit.prevent="devolverPrestamo">
      <v-card>
        <v-card-title>
          <span class="headline">Realizar Devolución</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="devolverItem.rutSolicitante" style="text-align:center;">Solicitante : {{devolverItem.rutSolicitante}}</v-card-text>
          </v-card>
        </v-flex>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="devolverItem.usuarioResponsable" style="text-align:center;">Responsable : {{devolverItem.usuarioResponsable}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="devolverItem.hora" style="text-align:center;">Hora : {{devolverItem.hora}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="devolverItem.fecha" style="text-align:center;">Fecha : {{devolverItem.fecha}}</v-card-text>
          </v-card>
        </v-flex>
                <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="devolverItem.motivo" style="text-align:center;">Motivo : {{devolverItem.motivo}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content v-for="item in this.itemPrestado">
        <div slot="header">Item : {{ item.id_item.nombre }}</div>
        <v-card>
          <v-card-text class="light-green lighten-3">Cantidad : {{ item.cantidad }}</v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="clearEditModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat>Devolver</v-btn>
        </v-card-actions>
      </v-card>
       </form>
    </v-dialog>
    <!-- Fin Dialog Devolver Prestamo -->
    <!-- Dialog Detalle Prestamo -->
       <v-dialog v-model="dialogDetail" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Detalle Prestamo</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="detailItem.rutSolicitante" style="text-align:center;">Solicitante : {{detailItem.rutSolicitante}}</v-card-text>
          </v-card>
        </v-flex>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="detailItem.usuarioResponsable" style="text-align:center;">Responsable : {{detailItem.usuarioResponsable}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="detailItem.hora" style="text-align:center;">Hora : {{detailItem.hora}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="detailItem.fecha" style="text-align:center;">Fecha : {{detailItem.fecha}}</v-card-text>
          </v-card>
        </v-flex>
                <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="detailItem.motivo" style="text-align:center;">Motivo : {{detailItem.motivo}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content v-for="item in this.itemPrestado">
        <div slot="header">Item : {{ item.id_item.nombre }}</div>
        <v-card>
          <v-card-text class="light-green lighten-3">Cantidad : {{ item.cantidad }}</v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
        
              
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Fin Dialog Detalle Prestamo -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Herramientas No devueltas</h1>
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
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="devolverModal(props.item)">
            <v-icon color="deep-orange darken-1">settings_backup_restore</v-icon>
          </v-btn>
          <span>Devolver</span>
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
      this.cargarSelectCat()
    },
    methods: {
      itemEliminado (e) {
        console.log(e)
        var indexArray = this.inputArray.findIndex(x => x.idInput === e)
        this.inputArray.splice(indexArray, 1)
        console.log(this.inputArray)
      },
      changeSelectPrestamo (e) {
        this.selectItemPrestamo = e.id
      },
      onClickChild (value) {
        this.inputArray.push(value)
        console.log(this.inputArray) // someValue
      },
      agregarItemPrestado () {
        var html1 = '<item></item>'
        var z = document.createElement('item')
        z.innerHTML = html1
        document.body.appendChild(z)
        document.getElementById('divItems').insertAdjacentHTML('afterend', html1)
      },
      onSelectItem (val) { // traer la cantidad del item a pedir
        axios.get(config.API_LOCATION + `/bodega/item/` + this.addItem.itemID.id + '')// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            console.log(response.data)
          })
          .catch(e => {
          })
        // this.cambioLabel1()
      },
      cambioLabel1 (val) {
        this.labelCantidad = 'Disponible : ' + val + ''
      },
      cambioLabel2 (val) {
        var cantidad = this.addItem.stock - val.target.value
        this.cantidadInsumos = this.addItem.stock
        this.labelCantidad = 'Disponible : ' + cantidad + ''
      },
      onChangeSelect (val) {
        this.editedItem.categoria = val.target.value
      },
      cargarSelectCat () {
        axios.get(config.API_LOCATION + `/bodega/item/herramienta`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            console.log(response.data)
            this.categorias = response.data
          })
          .catch(e => {
          })
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/prestamo/valueFalse`) // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      agregarPrestamo (e) { // función para realizar un prestamo
        var rutSolicitanteF = e.target.elements.solicitante.value
        var motivoF = e.target.elements.motivo.value
        axios.post(config.API_LOCATION + '/bodega/prestamo/', { // petición POST a prestamo para realizar un prestamo
          rutSolicitante: '' + rutSolicitanteF + '', motivo: '' + motivoF + '', devolucion: '' + false + '', usuarioResponsable: 'Admin', fecha: '', hora: null
        })
          .then((response) => {
            for (var i = 0; i < this.inputArray.length; i++) {
              var idItem = parseInt(document.getElementById(this.inputArray[i].idInput).getAttribute('value'))
              var idCantidad = parseInt(document.getElementById(this.inputArray[i].idCantidad).value)
              console.log(idCantidad)
              axios.post(config.API_LOCATION + '/bodega/detalle_prestamo/', { // petición POST a Subcategoría para agregar usuarioResponsable
                cantidad: idCantidad, id_item: {id: idItem}, id_prestamo: {id: response.data.id}
              })
                .then((response) => {
                  // Petición put editar ITEM
                  axios.get(config.API_LOCATION + '/bodega/item/' + idItem + '')
                    .then((responseGET) => {
                      var stockFinal = responseGET.data.stock - idCantidad
                      axios.put(config.API_LOCATION + '/bodega/item/' + idItem + '', {
                        nombre: responseGET.data.nombre,
                        id_marca: {id: responseGET.data.id_marca.id},
                        modelo: responseGET.data.modelo,
                        stock: stockFinal,
                        stockCritico: responseGET.data.stockCritico,
                        id_unidad_medida: {id: responseGET.data.id_unidad_medida.id},
                        id_subcategoria: {id: responseGET.data.id_subcategoria.id}
                      })
                        .then((responseGET) => {
                          // asd
                        })
                    })
                })
            }
            // this.items.push(response.data)// LLenado del array "items"
            this.initialize()
            this.dialogAdd = false // cerrar el modal
            this.snackbar = true
          })
      },
      devolverPrestamo () {
        var idPrestamo = this.devolverItem.id
        axios.put(config.API_LOCATION + '/bodega/prestamo/' + idPrestamo + '', {
          fecha: this.devolverItem.fecha,
          hora: this.devolverItem.hora,
          rutSolicitante: this.devolverItem.rutSolicitante,
          motivo: this.devolverItem.motivo,
          devolucion: true,
          fechaDevolucion: '',
          usuarioResponsable: this.devolverItem.usuarioResponsable
        })
          .then((responsePutPrestamo) => {
            axios.get(config.API_LOCATION + '/bodega/detalle_prestamo/' + idPrestamo + '/prestamo')
              .then((responseGetDpres) => {
                for (var i = 0; i < responseGetDpres.data.length; i++) {
                  var idItem = responseGetDpres.data[i].id_item.id
                  var stockFinal = responseGetDpres.data[i].cantidad + responseGetDpres.data[i].id_item.stock
                  axios.put(config.API_LOCATION + '/bodega/item/' + idItem + '', {
                    nombre: responseGetDpres.data[i].id_item.nombre,
                    id_marca: {id: responseGetDpres.data[i].id_item.id_marca.id},
                    modelo: responseGetDpres.data[i].id_item.modelo,
                    stock: stockFinal,
                    stockCritico: responseGetDpres.data[i].id_item.stockCritico,
                    id_unidad_medida: {id: responseGetDpres.data[i].id_item.id_unidad_medida.id},
                    id_subcategoria: {id: responseGetDpres.data[i].id_item.id_subcategoria.id}
                  })
                    .then((responseGetDpres) => {
                      this.initialize()
                      this.dialogDevolver = false
                    })
                }
              })
          })
          .catch(e => {
          })
      },
      devolverModal (item) {
        var id = item.id
        axios.get(config.API_LOCATION + '/bodega/detalle_prestamo/' + id + '/prestamo') // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.itemPrestado = response.data
            this.devolverIndex = this.items.indexOf(item) // obtener posición del array
            this.devolverItem = Object.assign({}, item)
            this.dialogDevolver = true
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