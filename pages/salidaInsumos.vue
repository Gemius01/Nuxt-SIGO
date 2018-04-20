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
    <!-- SnackBar (mensaje de Success)-->
 <v-snackbar
      :timeout="timeoutAdvertencia"
      :color="colorAdvertencia"
      :multi-line="modeAdvertencia === 'multi-line'"
      :vertical="modeAdvertencia === 'vertical'"
      v-model="snackbarAdvertencia"
    >
      {{ textAdvertencia }}
      <v-btn dark flat @click.native="snackbarAdvertencia = false">Cerrar</v-btn>
    </v-snackbar>
    <!-- Fin de SnackBar-->
  <!-- Dialog  Realizar Prestamo -->
    <v-dialog v-model="dialogAdd"  max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Generar Salida</v-btn>
      <v-form @submit.prevent="realizarSalida" ref="fRealizarSalida" v-model="valid" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Salida</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap >
              <v-flex xs12 sm12 md12>
                <v-text-field label="RUT Solicitante"  :rules="[v => !!v || 'Campo vacío', v => this.rutValidado || 'Rut Invalido']" v-on:keyup="checkRut" :counter="20" name="solicitante" v-model="addItem.solicitante" required></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Motivo"  :rules="textoRules" :counter="20" name="motivo" v-model="addItem.motivo" required></v-text-field>
              </v-flex>
              <v-flex xs12>
              <v-data-table
              :headers="headersItem"
              :items="itemsASalir"
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
                  <v-btn icon slot="activator" class="mx-0" @click="eliminarItemModal(props.item)">
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
          </v-flex>
          <v-flex xs12>
          <v-btn block dark color="primary" @click="agregarItemModal">
          AGREGAR ITEM
          </v-btn>
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
    <v-dialog v-model="agregarItem" max-width="500px">
      <v-form @submit.prevent="agregarItemASalir" v-model="valid" ref="fagregarItem" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Agregar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-select
              :items="itemsSelectSalida"
              item-text="nombre"
              item-value="id"
              v-model="selectItem"
              v-on:change="onChangeSelectAgregar"
              search-input
              :rules="[v => this.selectValidado || 'Campo Vacío']"
              :error-messages="errorMessages"
              required
              autocomplete
              label="Item"
              single-line
              ></v-select>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number" :rules="numberRules" id="cantidad" v-model="addItem.cantidad" required></v-text-field>
              </v-flex>
              <v-flex xs12 class="ordenDesc"  >
                   <h4>Stock Disponible: </h4><h4 v-model="stockActual">{{ stockActual }}</h4>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="clearAgregarItemModal">Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
    <!-- Dialog Editar Item a Prestar -->
    <v-dialog v-model="editarItemModal" max-width="500px">
      <v-form @submit.prevent="editarItemASalir" v-model="valid" ref="fEditarItemModal" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Editar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs6>
              <v-text-field label="Item" v-model="editItemSalir.item.nombre" required disabled></v-text-field>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number" :rules="numberRules" v-model="editItemSalir.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="clearEditarItemModal">Cancelar</v-btn>
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
              <v-list-tile-title>{{ detailItemSalida.id }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Marca</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.id_marca.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Modelo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.modelo }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock Critico</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.stockCritico }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.stock }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Subcategoria</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.id_subcategoria.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Unidad de medida</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemSalida.id_unidad_medida.medida }}</v-list-tile-title>
              </v-list-tile>
              </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="detalleItemModal = false">Cerrar</v-btn>
      </v-card-actions>
      </v-card>
    </v-dialog>
    <!--Fin Dialog Detalle Item a Prestar -->
    <!-- Dialog Eliminar Item Salida -->
    <v-dialog v-model="eliminarItemModalTable" max-width="500px">
      <v-form @submit.prevent="eliminarItem" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">¿Estás seguro de quitar el giro?</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
               <v-card>
                <v-list dense >
                  
                    <v-list-tile class="hoverMouse" xs12>
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ deleteItem.item.nombre }}</v-list-tile-title>
                  </v-list-tile>
                
                </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="eliminarItemModalTable = false">Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Quitar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
<!-- Fin Dialog Eliminar Item Salida -->
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
      <v-expansion-panel-content v-for="(item, i) in this.itemPrestado" :key="i">
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
        <form @submit.prevent="">
      <v-card>

      </v-flex>
      <v-card>
          <v-card-title><h1> Detalle de Salida</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
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
           </v-list>
        </v-card>
            </v-layout>
          </v-container>
        </v-card-text>
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
        <h1>SALIDA DE INSUMOS</h1>
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
        <!--boton devolver
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="devolverModal(props.item)">
            <v-icon color="deep-orange darken-1">settings_backup_restore</v-icon>
          </v-btn>
          <span>Devolver</span>
        </v-tooltip>-->
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
  import item from '../components/salidaInsumo/item.vue'
  import validaciones from '../validaciones.vue'
  export default {
    components: { config, item },
    data: () => ({
      headers: [ // Encabezados de  la tabla
        { text: 'Solicitante', value: 'rutSolicitante', width: '20%', align: 'center' },
        { text: 'Responsable', value: 'usuarioResponsable', width: '20%', align: 'center' },
        { text: 'Hora', value: 'hora', width: '20%', align: 'center' },
        { text: 'Fecha', value: 'fecha', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      headersItem: [ // Encabezados de  la tabla
        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
        { text: 'Cantidad', value: 'cantidad', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
      textoRules: validaciones.textoRules,
      numberRules: validaciones.numberRules,
      errorMessages: [],
      itemsASalir: [],
      stockActual: '',
      itemsSelectSalida: [],
      paginationItems: {},
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Subcategoría
      dialogDevolver: false, // prop para abrir y cerrar modal de Editar Subcategoría
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Subcategoría
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Subcategoría
      editarItemModal: false,
      detalleItemModal: false,
      eliminarItemModalTable: false,
      agregarItem: false,
      selectValidado: false,
      rutValidado: false,
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      devolverIndex: -1,
      labelCantidad: 'Disponible : ' + 0 + '',
      categoriaSelectID: {},
      categoriaSelectIDEdit: {},
      value: '',
      selectItem: 0,
      editItemSalir: {item: {id: 1, nombre: 'asd', marca: ''}, cantidad: 0},
      search: '',
      inputArray: [{idInput: 'selectItemPrestamo', idCantidad: 'cantidad'}],
      // SnackBar Exitoso
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      //
      // SnackBar Advertencia
      snackbarAdvertencia: false,
      colorAdvertencia: 'green',
      modeAdvertencia: '',
      timeoutAdvertencia: 3000,
      textAdvertencia: 'Se ha agregado con exito',
      categorias: [],
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
      detailItemSalida: {
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stockCritico: '',
        stock: ''
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
        item: {
          id: 0,
          nombre: '',
          modelo: '',
          id_marca: '',
          id_subcategoria: '',
          id_unidad_medida: '',
          stockCritico: '',
          stock: ''
        },
        cantidad: 0
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
      this.cargarItems()
    },
    methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/salida/`) // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      checkRut: function (rut) {
        var valorOriginal = rut.target.value.replace('.', '')
        var valor = rut.target.value.replace('.', '')
        var cuerpo = null
        var dv = null
        for (var j = 1; j <= valorOriginal.length; j++) {
          valor = valor.replace('.', '')
          valor = valor.replace('-', '')
        }
        // Despejar Guión
        valor = valor.replace('-', '')
        // Aislar Cuerpo y Dígito Verificador
        cuerpo = valor.slice(0, -1)
        dv = valor.slice(-1).toUpperCase()
        // Formatear RUN
        rut.value = cuerpo + '-' + dv
        // Si no cumple con el mínimo ej. (n.nnn.nnn)
        if (cuerpo.length < 7) { this.rutValidado = false } else {
          var suma = 0
          var multiplo = 2

          // Para cada dígito del Cuerpo
          for (var i = 1; i <= cuerpo.length; i++) {
          // Obtener su Producto con el Múltiplo Correspondiente
            var index = multiplo * valor.charAt(cuerpo.length - i)

            // Sumar al Contador General
            suma = suma + index

            // Consolidar Múltiplo dentro del rango [2,7]
            if (multiplo < 7) { multiplo = multiplo + 1 } else { multiplo = 2 }
          }
          // Calcular Dígito Verificador en base al Módulo 11
          var dvEsperado = 11 - (suma % 11)
          // Casos Especiales (0 y K)
          dv = (dv === 'K') ? 10 : dv
          dv = (dv === 0) ? 11 : dv
          // Validar que el Cuerpo coincide con su Dígito Verificador

          // console.log('dv:' + dv + 'dvEsperado:' + dvEsperado)
          if (parseInt(dvEsperado) !== parseInt(dv)) { this.rutValidado = false } else {
            this.rutValidado = true
          }
        }
        // Calcular Dígito Verificador
      },
      eliminarItemModal (item) {
        this.deleteItem = item
        this.eliminarItemModalTable = true
      },
      onChangeSelectAgregar (e) {
        if(e!==null){
          axios.get(config.API_LOCATION + '/bodega/item/' + e + '')
            .then((response) => {
              this.stockActual = response.data.stock
            })
            .catch(e => {
            })
          this.selectValidado = true
        }

      },
      detalleItem (obj) {
        axios.get(config.API_LOCATION + '/bodega/item/' + obj.item.id + '')
          .then((response) => {
            this.detailItemSalida = response.data
            this.detalleItemModal = true
          })
          .catch(e => {
          })
      },
      changeSelectPrestamo (e) {
        this.selectItemPrestamo = e.id
      },
      agregarItemASalir () {
        var idItem = this.selectItem
        var cantidaItem = document.getElementById('cantidad').value
        var item = []
        if (this.$refs.fagregarItem.validate()) {
          axios.get(config.API_LOCATION + '/bodega/item/' + idItem + '')
            .then((response) => {
              var stockCritico = response.data.stockCritico
              item = response.data
              this.itemsASalir.push({item: item, cantidad: cantidaItem})
              var indexArray = this.itemsSelectSalida.findIndex(x => x.id === item.id)
              console.log(indexArray)
              this.itemsSelectSalida.splice(indexArray, 1)
              console.log(this.itemsSelectSalida)
              this.agregarItem = false
              var advertenciaStock = this.stockActual - cantidaItem
              console.log(advertenciaStock)
              if (stockCritico >= advertenciaStock) {
                console.log('entre')
                this.colorAdvertencia = 'orange darken-4'
                this.textAdvertencia = 'Advertencia "' + response.data.nombre + '" con Stock Crítico: ' + advertenciaStock + ''
                this.snackbarAdvertencia = true
              }

            })
            .catch(idItem => {
            })
        }
      },
      editarItemASalir () {
        if (this.$refs.fEditarItemModal.validate()) {
          Object.assign(this.itemsASalir[this.editedIndex], this.editItemSalir)
          this.text = 'Se ha editado correctamente'
          this.snackbar = true
          this.editarItemModal = false
        }
      },
      cargarItems () {
        axios.get(config.API_LOCATION + '/bodega/item/insumo')
          .then((response) => {
            this.itemsSelectSalida = response.data
          })
          .catch(e => {
          })
      },
      editItemModal (item) {
        this.editItemSalir = item
        this.editedIndex = this.itemsASalir.indexOf(item)
        this.editarItemModal = true

      },
      agregarItemPrestado () {
        var html1 = '<item></item>'
        var z = document.createElement('item')
        z.innerHTML = html1
        document.body.appendChild(z)
        document.getElementById('divItems').insertAdjacentHTML('afterend', html1)
      },
      realizarSalida (e) { // función para realizar un prestamo
        var rutSolicitanteF = e.target.elements.solicitante.value
        var motivoF = e.target.elements.motivo.value
        if (this.$refs.fRealizarSalida.validate()) {
          if(this.itemsASalir.length <= 0){
            alert("Debe Agregar Items")
          }
          else{
            axios.post(config.API_LOCATION + '/bodega/salida/', {
              rutSolicitante: '' + rutSolicitanteF + '', motivo: '' + motivoF + '', usuarioResponsable: 'Admin', fecha: '', hora: null
            })
              .then((responseSalida) => {
                for (var i = 0; i < this.itemsASalir.length; i++) {
                  var idItem = this.itemsASalir[i].item.id
                  var idCantidad = this.itemsASalir[i].cantidad
                  axios.post(config.API_LOCATION + '/bodega/detalle_salida/', {
                    cantidad: idCantidad, id_item: {id: idItem}, id_salida: {id: responseSalida.data.id}
                  })
                    .then((response) => {
                      // Petición put editar ITEM
                      axios.get(config.API_LOCATION + '/bodega/item/' + idItem + '')
                        .then((responseGET) => {
                          var stockFinal = responseGET.data.stock - idCantidad
                          console.log(stockFinal)
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
                this.text = 'Se ha generado correctamente'
                this.snackbar = true
              })
          }
        }
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
      agregarItemModal () {
        this.agregarItem = true
      },
      close () {
        this.dialog = false
      },
      eliminarItem () {
        var item = this.deleteItem
        var indexArray = this.itemsASalir.findIndex(x => x.item.id === item.item.id)
        this.itemsASalir.splice(indexArray, 1)
        this.itemsSelectSalida.push(item.item)
        this.text = 'Se ha quitado correctamente'
        this.snackbar = true
        this.eliminarItemModalTable = false
      },
      clearAddModal () {
        // this.$refs.fAgregarSubCat.reset()
        this.dialogAdd = false
      },
      clearEditModal () {
        this.dialogEdit = false
      },
      clearAgregarItemModal () {
        this.agregarItem = false
        this.$refs.fagregarItem.reset()
      },
      clearEditarItemModal () {
        this.editarItemModal = false
      },
      modalDetalle (item) {
        var id = item.id
        axios.get(config.API_LOCATION + '/bodega/detalle_salida/' + id + '/salida') // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
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
<style>
  .ordenDesc{
    background-color: orange;
    border-radius: 3px;
    text-align: center;
  }
</style>
