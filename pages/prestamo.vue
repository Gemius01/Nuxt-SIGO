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
    <v-dialog v-model="dialogAdd"  max-width="500px">
      <v-btn dark color="primary"  slot="activator" >Realizar Prestamo</v-btn>
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
          <v-flex xs12 id="divItems">
            <v-data-table
              :headers="headersItem"
              :items="itemsAPrestar"
              :search="search"
              must-sort
              :pagination.sync="paginationItems"
              class="elevation-1"
              >
            <!-- Fin Tabla-->
            <template slot="items" slot-scope="props">
              <td class="text-xs-center">{{ props.item.item.nombre }}</td>
              <td class="text-xs-center">{{ props.item.cantidad }}</td>
              <td class="justify-center layout px-0">
                <v-tooltip top>
                  <v-btn icon slot="activator" class="mx-0" @click="detalleItem(props.item)" >
                  <v-icon color="blue">search</v-icon>
                  </v-btn>
                  <span>Detalle</span>
                </v-tooltip>
                <v-tooltip top>
                  <v-btn icon slot="activator" class="mx-0" @click="editItemModal(props.item)">
                  <v-icon color="green">edit</v-icon>
                  </v-btn>
                  <span>Editar</span>
                </v-tooltip>
                <v-tooltip top>
                  <v-btn icon slot="activator" class="mx-0" @click="eliminarItem(props.item)">
                  <v-icon color="deep-orange darken-1">delete</v-icon>
                  </v-btn>
                  <span>Quitar</span>
                </v-tooltip>
              </td>
            </template>
            <template slot="pageText" slot-scope="{ pageStart, pageStop }">
            De {{ pageStart }} a {{ pageStop }}
            </template>
            </v-data-table>
          <v-flex xs12 >
          <v-btn block dark color="primary" @click="agregarItemModal">
          AGREGAR ITEM
          </v-btn>
          </v-flex>
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
    <!-- Dialog Agregar Item a Prestar -->
    <v-dialog v-model="agregarItem" max-width="500px">
      <v-form @submit.prevent="agregarItemAPrestar" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Agregar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-select
              id="selectItemPrestamo"
              :items="itemsSelectPrestar"
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
              <v-text-field label="Cantidad" type="number" id="cantidad" v-model="addItem.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat >Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
<!-- Fin Dialog Agregar Item a Prestar -->
<!-- Dialog Editar Item a Prestar -->
    <v-dialog v-model="editarItemModal" max-width="500px">
      <v-form @submit.prevent="editarItemAprestar" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Editar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs6>
              <v-text-field label="Item" v-model="editItemPrestar.item.nombre" required disabled></v-text-field>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number"  v-model="editItemPrestar.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat >Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
<!-- Fin Dialog Editar Item a Prestar -->
<!-- Detalle Item a Prestar -->
    <v-dialog v-model="detalleItemModal" max-width="500px">
      <v-card>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
              <v-card>
              <v-card-title><h1> Detalle de Insumo</h1></v-card-title>
              <v-divider></v-divider>
              <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.id }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Marca</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.id_marca.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Modelo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.modelo }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock Critico</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.stockCritico }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.stock }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Subcategoria</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.id_subcategoria.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Unidad de medida</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemPrestar.id_unidad_medida.medida }}</v-list-tile-title>
              </v-list-tile>
              </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="detalleItemModal = false">Cancelar</v-btn>
      </v-card-actions>
      </v-card>
    </v-dialog>
    <!--Fin Dialog Detalle Item a Prestar -->
    <!-- Dialog Devolver Prestamo -->
   <v-dialog v-model="dialogDevolver" max-width="500px">
      <form @submit.prevent="devolverPrestamo">
      <v-card>
         <v-card-title><h1> Detalle de Salida</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>RUT Solicitante</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ devolverItem.rutSolicitante }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Usuario Responsable</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ devolverItem.usuarioResponsable }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Hora</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ devolverItem.hora }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ devolverItem.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Motivo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ devolverItem.motivo }}</v-list-tile-title>
            </v-list-tile>
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
          </v-list>

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
              <v-list-tile-title>{{ detailItem.hora }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
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
      <v-expansion-panel-content v-for="item in this.itemPrestado">
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
        <h1>Prestamos</h1>
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
  export default {
    components: { config },
    data: () => ({
      headers: [ // Encabezados de  la tabla
        { text: 'Solicitante', value: 'rutSolicitante', width: '20%', align: 'center' },
        { text: 'Responsable', value: 'usuarioResponsable', width: '20%', align: 'center' },
        { text: 'Hora', value: 'hora', width: '20%', align: 'center' },
        { text: 'Fecha', value: 'fecha', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      headersItem: [ // Encabezados de  la tabla
        { text: 'Nombre', value: 'nombre', width: '20%', align: 'center' },
        { text: 'Cantidad', value: 'cantidad', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      textoRules: config.rules, // Reglas de los TXT para validar
      errorMessages: [], // label para mostrar errores
      agregarItem: false, // modal para agregar item a prestar
      editarItemModal: false, // modal para editar un item a prestar
      detalleItemModal: false, // modal detalle de un item a prestar
      dialogAdd: false, // modal para realizar un prestamo
      dialogDevolver: false, // modal para devolver items prestados
      dialogDetail: false, // modal de detalle de prestamo
      pagination: {}, // paginación de la tabla de prestamos
      paginationItems: {}, // paginacion de tabla items a prestar
      editedIndex: -1, // index de item para editar
      devolverIndex: -1,
      value: '',
      selectItem: 0,
      search: '',
      snackbar: false, // SnackBar Open/Close
      color: 'green', // Color de SnackBar
      mode: '', // Modo SnackBar
      timeout: 3000, // Tiempo que dura el SnackBar
      itemsSelectPrestar: [], // Array con items
      text: 'Se ha agregado con exito',
      categorias: [],
      itemsAPrestar: [],
      items: [], // Items de la tabla prestamos
      valid: true,
      cantidadInsumos: null,
      editItemPrestar: {item: {id: 1, nombre: 'asd', marca: ''}, cantidad: 0},
      selectEditItem: [],
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
      detailItemPrestar: {
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stockCritico: '',
        stock: ''
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
      }
    },
    created () {
      this.initialize() // cargar datos tabla prestamo al iniciar la ruta
      this.cargarItems() // cargar items para prestar
    },
    methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/prestamo/valueFalse`)
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      detalleItem (obj) {
        axios.get(config.API_LOCATION + '/bodega/item/' + obj.item.id + '')
          .then((response) => {
            this.detailItemPrestar = response.data
            this.detalleItemModal = true
          })
          .catch(e => {
          })
      },
      editarItemAprestar (e) {
        Object.assign(this.itemsAPrestar[this.editedIndex], this.editItemPrestar)
        this.editarItemModal = false
      },
      editItemModal (item) {
        this.editItemPrestar = item
        this.editedIndex = this.itemsAPrestar.indexOf(item)
        this.editarItemModal = true
      },
      cargarItems () {
        axios.get(config.API_LOCATION + '/bodega/item/herramienta')
          .then((response) => {
            this.itemsSelectPrestar = response.data
          })
          .catch(e => {
          })
      },
      agregarItemAPrestar (e) {
        var item = this.selectItem
        var cantidaItem = document.getElementById('cantidad').value
        this.itemsAPrestar.push({item: item, cantidad: cantidaItem})
        var indexArray = this.itemsSelectPrestar.findIndex(x => x.id === item.id)
        this.itemsSelectPrestar.splice(indexArray, 1)
        this.agregarItem = false
      },
      agregarItemModal () {
        this.agregarItem = true
      },
      changeSelectPrestamo (e) {
        this.selectItemPrestamo = e.id
      },
      agregarPrestamo (e) { // función para realizar un prestamo
        var rutSolicitanteF = e.target.elements.solicitante.value
        var motivoF = e.target.elements.motivo.value
        axios.post(config.API_LOCATION + '/bodega/prestamo/', { // petición POST a prestamo para realizar un prestamo
          rutSolicitante: '' + rutSolicitanteF + '', motivo: '' + motivoF + '', devolucion: '' + false + '', usuarioResponsable: 'Admin', fecha: '', hora: null
        })
          .then((response) => {
            for (var i = 0; i < this.itemsAPrestar.length; i++) {
              var idItem = this.itemsAPrestar[i].item.id
              var idCantidad = this.itemsAPrestar[i].cantidad
              axios.post(config.API_LOCATION + '/bodega/detalle_prestamo/', {
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
                        })
                    })
                })
            }
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
      eliminarItem (item) {
        var indexArray = this.itemsAPrestar.findIndex(x => x.item === item)
        this.itemsAPrestar.splice(indexArray, 1)
        this.itemsSelectPrestar.push(item.item)
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