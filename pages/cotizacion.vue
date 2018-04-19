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
  <!-- Dialog  Realizar Cotización -->
    <v-dialog v-model="dialogAdd"  max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Crear Cotización</v-btn>
            <v-form @submit.prevent="agregarCotizacion" v-model="valid" ref="fAgregarSubCat" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Ingresar Cotización</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap >
              <v-flex s12>
                <v-select
                id="selectOrdenCompra"
                label="Orden de Compra"
                single-line
                :items="ordenesCompra"
                item-id="id"
                item-text="id"
                :value="selectOrdenCompra.id"
                @change="changeSelectOrdenCompra"
                v-model="selectOrdenCompra"
                ></v-select>
                <v-flex s12 class="divItemCotizacion">
                  <h4 class="textDivCotizacion">Cotizacion</h4>
                  <v-flex xs12>
                    <v-text-field label="Fecha" value="03/12/2018" disabled></v-text-field> 
                  </v-flex>
                  <v-select
                  id="selectProveedor"
                  :items="proveedores"
                  item-text="nombre"
                  label="Proveedor"
                  :item-id="selectProveedor.rut"
                  :value="selectProveedor.rut"
                  v-model="selectProveedor"
                  
                  single-line
                  ></v-select>
                  <v-flex xs12>
                    <v-text-field label="Total Neto" id="txtTotalNeto" v-model="txtValorNeto"></v-text-field> 
                  </v-flex>
                  <item :itemsOrden="itemsOrden" @inputValores="pushInputValores"></item>
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
    <!-- Fin Dialog Realizar Cotizacion -->
    <!-- Dialog Asignar Cotización -->
       <v-dialog v-model="dialogDevolver" max-width="500px">
        <form @submit.prevent="devolverPrestamo">
      <v-card>
        <v-card-title>
          <span class="headline">Asignar Cotización</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            <v-flex xs12>
            <v-select
            id="selectOrdenCompraAsignar"
                label="Orden de Compra"
                single-line
                :items="ordenesCompra"
                item-id="id"
                item-text="id"
          ></v-select>
          </v-flex>
          <v-flex xs12>
              <cotizaciones></cotizaciones>
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

          <v-card-title><h1> Detalle de la Cotización </h1></v-card-title>
          <v-divider></v-divider>

          <v-list dense >
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Proveedor</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rut_proveedor.rut }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Rut Proveedor</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rut_proveedor.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Representante</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rut_proveedor.representante }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Telefono</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rut_proveedor.fono }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Total Neto</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.total_neto }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title><h4>Item/s :</h4></v-list-tile-title>
              <v-list-tile-title class="text-lg-center"></v-list-tile-title>
              <v-list-tile-title></v-list-tile-title>
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
        <h1>Cotizaciones</h1>
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
        <td class="text-xs-center">{{ props.item.rut_proveedor.rut }}</td>
        <td class="text-xs-center">{{ props.item.rut_proveedor.nombre }}</td>
        <td class="text-xs-center">{{ props.item.rut_proveedor.fono }}</td>
        <td class="text-xs-center">{{ props.item.total_neto }}</td>
        <td class="justify-center layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
            <v-icon color="blue">search</v-icon>
          </v-btn>
          <span>Detalle</span>
          </v-tooltip>
          <!--
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="devolverModal(props.item)">
            <v-icon color="deep-orange darken-1">settings_backup_restore</v-icon>
          </v-btn>
          <span>Devolver</span>
          </v-tooltip>
        -->
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
  import item from '../components/cotizacion/item.vue'
  import cotizaciones from '../components/cotizacion/cotizaciones.vue'
  export default {
    components: { config, item, cotizaciones },
    data: () => ({
      textoRules: config.rules,
      selectOrdenCompra: 0,
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Subcategoría
      dialogDevolver: false, // prop para abrir y cerrar modal de Editar Subcategoría
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Subcategoría
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Subcategoría
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      ordenesCompra: [],
      itemsOrden: [],
      deleteIndex: -1,
      devolverIndex: -1,
      labelCantidad: 'Disponible : ' + 0 + '',
      txtValorNeto: 0,
      categoriaSelectID: {},
      categoriaSelectIDEdit: {},
      value: '',
      selectItem: 0,
      search: '',
      inputArray: [{idInput: 'selectItemPrestamo', idCantidad: 'cantidad', idValor: 'idValor'}],
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      categorias: [],
      headers: [ // Encabezados de  la tabla
        { text: 'Rut Proveedor', value: 'rut_proveedor.rut', width: '20%', align: 'center' },
        { text: 'Nombre', value: 'rut_proveedor.nombre', width: '20%', align: 'center' },
        { text: 'Fono', value: 'rut_proveedor.fono', width: '20%', align: 'center' },
        { text: 'Total Neto', value: 'total_neto', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      items: [],
      incompleta: false,
      proveedores: [],
      selectProveedor: 0,
      valid: true,
      inputValores: [],
      inputItemsCotizados: [],
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
        fecha: '',
        rut_proveedor: {
          rut: '',
          nombre: '',
          representante: '',
          direccion: '',
          id_ciudad: {
            id: 0,
            nombre: '',
            id_region: {
              id: 0,
              nombre: '',
              id_pais: {
                id: 0,
                nombre: ''
              }
            }
          },
          fono: '',
          movil: '',
          email: '',
          nacional: false
        },
        total_neto: 0
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
      },
      inputValores (e) {
        console.log(e)
      }
    },
    created () {
      this.initialize()
      this.cargarSelectOrdenCompra()
      this.cargarSelectProveedor()
    },
    methods: {
      pushInputValores (e) {
        this.inputValores.push(e)
      },
      itemEliminado (e) {
        console.log(e)
        var indexArray = this.inputArray.findIndex(x => x.idInput === e)
        this.inputArray.splice(indexArray, 1)
        console.log(this.inputArray)
      },
      changeSelectOrdenCompra (e) {
        console.log(e)
        axios.get(config.API_LOCATION + `/bodega/detalle_adquisicion/` + e.id + '/orden')// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.itemsOrden = response.data
          })
          .catch(e => {
          })
      },
      onClickChild (value) {
        this.itemsOrden.push(value)
        console.log(this.inputArray) // someValue
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
      cargarSelectOrdenCompra () {
        axios.get(config.API_LOCATION + `/bodega/orden_compra/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.ordenesCompra = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectProveedor () {
        axios.get(config.API_LOCATION + `/bodega/proveedor/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.proveedores = response.data
          })
          .catch(e => {
          })
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/cotizacion/`) // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      agregarCotizacion (e) { // función para realizar un prestamo
        var idSelectOrden = parseInt(document.getElementById('selectOrdenCompra').getAttribute('value'))
        var idProveedor = document.getElementById('selectProveedor').getAttribute('value')
        console.log('' + idSelectOrden + ' ' + idProveedor + ' ')
        var totalNeto = this.txtValorNeto
        for (var i = 0; i < this.inputValores.length; i++) {
          var inputVal = document.getElementById(this.inputValores[i].idValor).value
          console.log(inputVal)
          if (inputVal) { } else { this.incompleta = true }
        }
        axios.post(config.API_LOCATION + '/bodega/cotizacion/', {
          fecha: '',
          incompleta: this.incompleta,
          total_neto: totalNeto,
          orden_compra: idSelectOrden, 
          rut_proveedor: {rut: '' + idProveedor + ''}
        })
          .then((response) => {
            console.log(response.data.id)
            for (var i = 0; i < this.inputValores.length; i++) {
              var inputValUnit = parseInt(document.getElementById(this.inputValores[i].idValor).value)
              var inputCantidad = document.getElementById(this.inputValores[i].inputCantidad).value
              var inputIdItem = document.getElementById(this.inputValores[i].idItem).value
              axios.post(config.API_LOCATION + '/bodega/detalle_cotizacion/', {cantidad: inputCantidad, valor_unitario: inputValUnit, id_cotizacion: {id: response.data.id}, id_item: {id: inputIdItem}})
                .then((response) => {
                  console.log('saliiii')
                })
                .catch(e => {
                })
            }
            this.initialize()
            this.dialogAdd = false
          })
          .catch(e => {
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
        console.log(id)
        axios.get(config.API_LOCATION + '/bodega/detalle_cotizacion/' + id + '/cotizacion') // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
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
  div .divItemCotizacion{
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
</style>