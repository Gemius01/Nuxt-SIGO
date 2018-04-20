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
    <!-- Dialog Eliminar Tipo -->
    <v-dialog v-model="dialogDelete" persistent max-width="350">

      <v-card>
        <v-card-title class="headline">¿Está seguro de eliminar?</v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.id" style="text-align:center;">ID : {{deleteItem.id}}</v-card-text>
          </v-card>
        </v-flex>
         <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.nombre" style="text-align:center;">Nombre : {{deleteItem.nombre}}</v-card-text>
          </v-card>
        </v-flex>

            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialogDelete = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="deleteHerramienta">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Tipo-->
  <!-- Dialog Agregar Tipo -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Herramientas</v-btn>
            <v-form @submit.prevent="agregarHerramientas" v-model="valid" ref="fAgregarHerramientas" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Herramientas</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
                <v-flex xs12>
                <v-text-field label="Nombre"  :rules="textoRules" :counter="20" name="nombreHerramienta" v-model="addItem.nombre" required></v-text-field>
                </v-flex>
                <v-flex xs12>
                <v-text-field label="Modelo"  :rules="textoRules" :counter="20" name="modeloHerramienta" v-model="addItem.modelo" required></v-text-field>
                </v-flex>
                <v-flex xs12>
                <v-text-field label="Stock Critico" :rules="numberRule" :counter="20" name="stockCriticoHerramienta" v-model="addItem.stockCritico" type="number" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6 md11>
                <v-select item-value="id" item-text="nombre" :items="marcaItem" v-model="addItem.marca" label="Marca" single-line :rules="[v => this.selectValidado || 'Campo vacío']" v-on:change='onChangeMarca'
                ></v-select>
                </v-flex>
                <v-flex xs12 sm6 md1>
                <v-btn  @click.stop="dialog3 = !dialog3" icon small slot="activator" class="mx-0" fab dark color="indigo"><v-icon dark>add</v-icon></v-btn></v-flex>
                </v-flex>
                <v-flex xs12 sm6>
                <v-select item-value="id" item-text="medida" :items="unidadMedidaItem" v-model="addItem.unidaddemedida" label="Unidad de medida" single-line :rules="[v => this.selectValidado2 || 'Campo vacío']" v-on:change='onChangeUnidad'
                ></v-select>
                </v-flex>
                <v-flex xs12 sm6>
                <v-select item-value="id" item-text="nombre" :items="subcategoriasItem" v-model="addItem.subcatego" label="Subcategoria" single-line :rules="[v => this.selectValidado3 || 'Campo vacío']" v-on:change='onChangeSubcat'
                ></v-select>
                </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="clearAddModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Agregar Tipo -->
<v-dialog v-model="dialog3" max-width="500px">
            <v-form @submit.prevent="agregarMarca" v-model="valid" ref="fagregarMarca" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Marca</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Nombre"  :rules="textoRules" :counter="20" name="nombre" v-model="addItemMarca.nombre" required></v-text-field>
                </v-flex>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="marcaClose">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Dialog Editar Tipo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editHerramienta" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Herramienta</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 style="display:none;">
                <v-text-field label="Nombre" v-model="editedItem.id" name="idEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 style="display:none;">
                <v-text-field label="Stock" v-model="editedItem.stock" name="stockEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Nombre" :counter="20" :rules="textoRules" v-model="editedItem.nombre" name="nombreEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Modelo" :counter="20" :rules="textoRules" v-model="editedItem.modelo" name="modeloEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Stock Critico" type="number" :counter="20" :rules="numberRule" v-model="editedItem.stockCritico" name="stockCriticoEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md11>
                <v-select
                id="selectMarcaEdit"
                :items="marcaItem"
                item-text="nombre"
                item-value="id"
                @select='onChangeSelect0'
                v-model="editedItem.id_marca.id"
                :rules="[() => !!this.editedItem.id || 'This field is required']"
                search-input autocomplete label="Marca"
                single-line>
                </v-select>
        </v-flex>
              <v-flex xs12 sm6 md1>
                <v-btn  @click.stop="dialog3 = !dialog3" icon small slot="activator" class="mx-0" fab dark color="indigo"><v-icon dark>add</v-icon></v-btn></v-flex>

              <v-flex xs12>
                <v-select
                id="selectUnidadEdit"
                :items="unidadMedidaItem"
                item-text="medida"
                item-value="id"
                @select='onChangeSelect1'
                v-model="editedItem.id_unidad_medida.id"
                :rules="[() => !!this.editedItem.id || 'This field is required']"
                search-input autocomplete label="Unidad de medida"
                single-line>
                </v-select>
        </v-flex>
        <v-flex xs12>
                <v-select
                id="selectSubEdit"
                :items="subcategoriasItem"
                item-text="nombre"
                item-value="id"
                v-model="editedItem.id_subcategoria.id"
                @select='onChangeSelect'
                search-input
                autocomplete
                label="Subcategoria">
                </v-select>
        </v-flex>
        </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="clearEditModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat>Guardar</v-btn>
        </v-card-actions>
      </v-card>
    </v-form>
    </v-dialog>
    <!-- Fin Dialog Editar Tipo -->
    <!-- Dialog Detalle Tipo -->
        <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Herramienta</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Marca</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_marca.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Modelo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.modelo }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock Critico</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.stockCritico }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.stock }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Subcategoria</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_subcategoria.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Unidad de medida</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_unidad_medida.medida }}</v-list-tile-title>
            </v-list-tile>
          </v-list>
       
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Editar Tipo -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>LISTA DE HERRAMIENTAS</h1>
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
  import validaciones from '../validaciones.vue'
  export default {
    components: { config },
    data: () => ({
      subcatego: 0,
      subcategoriasItem: [],
      marca: 0,
      selectSubEdit: 0,
      marcaItem: [],
      unidaddemedida: 0,
      unidadMedidaItem: [],
      textoRules: validaciones.textoRules,
      numberRule: validaciones.numberRules,
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Tipo
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Tipo
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Tipo
      dialogDelete: false,
      dialog3: false, // prop para abrir y cerrar modal de Delete Tipo
      selectValidado: false,
      selectValidado2: false,
      selectValidado3: false,
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      search: '',
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      headers: [ // Encabezados de  la tabla
        {
          text: 'ID',
          value: 'id',
          sortable: true,
          width: '20%',
          align: 'center'
        },
        { text: 'Nombre', value: 'nombre', width: '20%', align: 'center' },
        { text: 'Stock', value: 'stock', width: '20%', align: 'center' },
        { text: 'Unidad de Medida', value: 'unidadmedida', width: '20%', align: 'center' },
        { text: 'Subcategoria', value: 'subcategoria', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      items: [],
      valid: true,
      addItem: {
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stock_critico: ''
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
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
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stockCritico: '',
        stock: ''
      },
      addItemMarca: {
        id: 0,
        nombre: ''
      },
      deleteItem: {
        id: 0,
        nombre: ''
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
      this.initialize()
      this.cargarSelectCat()
      this.cargarSelectUnidad()
      this.cargarSelectMarca()
    },
    methods: {
      onChangeSelect (val) {
        console.log(val)
        this.editedItem.id_subcategoria.nombre = val.target.value
      },
      onChangeSelect0 (val) {
        console.log(val)
        this.editedItem.id_marca.nombre = val.target.value
      },
      onChangeMarca (val) {
        this.selectValidado = true
      },
      onChangeUnidad (val) {
        this.selectValidado2 = true
      },
      onChangeSubcat (val) {
        this.selectValidado3 = true
      },
      onChangeSelect1 (val) {
        this.editedItem.id_unidad_medida.nombre = val.target.value
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/item/herramienta`) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.items = response.data// LLenado del array "items"
          })
          .catch(e => {
          })
      },
      cargarSelectMarca () {
        axios.get(config.API_LOCATION + `/bodega/marca/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "Herramienta"
          .then((response) => {
            this.marcaItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectCat () {
        axios.get(config.API_LOCATION + `/bodega/subcategoria/tipo/herramienta/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "herramienta"
          .then((response) => {
            this.subcategoriasItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectUnidad () {
        axios.get(config.API_LOCATION + `/bodega/unidad_medida/`)// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "herramienta"
          .then((response) => {
            this.unidadMedidaItem = response.data
          })
          .catch(e => {
          })
      },
      agregarHerramientas (e) { // función para agregar un nuevo Tipo
        var nombre = this.addItem.nombre
        var modelo = this.addItem.modelo
        var stockCri = this.addItem.stockCritico
        var stock = 0
        var marca = this.addItem.marca
        var unidadmedida = this.addItem.unidaddemedida
        var subCategoria = this.addItem.subcatego
        console.log(marca)
        console.log(unidadmedida)
        console.log(subCategoria)
        if (this.$refs.fAgregarHerramientas.validate()) {
          axios.post(config.API_LOCATION + '/bodega/item/', { // petición POST a Tipo para agregar
            nombre: '' + nombre + '',
            id_marca: { id: marca },
            modelo: '' + modelo + '',
            stock: '' + stock + '',
            stockCritico: '' + stockCri + '',
            id_subcategoria: { id: subCategoria },
            id_unidad_medida: { id: unidadmedida }
          }
          )
            .then((response) => {
              this.initialize() // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.text = 'Se ha agregado correctamente'
              this.snackbar = true
              this.$refs.fAgregarHerramientas.reset()
              this.selectValidado = false
              this.selectValidado2 = false
              this.selectValidado3 = false
            })
        }
      },
      editHerramienta (e) { // función para editar la Subcategoría
        var id = this.editedItem.id// obtener id del objeto que se desea editar formulario
        var nombre = this.editedItem.nombre// obtener nombre del objeto que se desea editar
        var modelo = this.editedItem.modelo
        var stockCritico = this.editedItem.stockCritico
        var marca = this.editedItem.id_marca.id
        var stock = this.editedItem.stock
        var unidadmedida = this.editedItem.id_unidad_medida.id
        console.log(marca)
        var subCategoria = this.editedItem.id_subcategoria.id
        console.log(subCategoria)
        if (this.$refs.fEditarHerramientas.validate()) {
          axios.put(config.API_LOCATION + '/bodega/item/' + id + '', {// petición put para editar el tipo
            nombre: '' + nombre + '',
            id_marca: { id: marca },
            modelo: '' + modelo + '',
            stock: '' + stock + '',
            stockCritico: '' + stockCritico + '',
            id_subcategoria: { id: '' + subCategoria + '' },
            id_unidad_medida: { id: '' + unidadmedida + '' }}
          )
            .then(response => {
              Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
              this.text = 'Se ha modificado correctamente'
              this.snackbar = true
              this.dialogEdit = false // cerrar modal
            })
            .catch(function (error) {
              console.log(error)
            })
        }
      },
      agregarMarca (e) { // función para agregar un nuevo Marca
        var Marca = this.addItemMarca.nombre
        if (this.$refs.fagregarMarca.validate()) {
          axios.post(config.API_LOCATION + '/bodega/marca/', { // petición POST a Marca para agregar
            nombre: '' + Marca + ''
          })
            .then((response) => {
              this.dialog3 = false
              this.cargarSelectMarca()
              this.snackbar = true
            })
        }
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      modalDelete (item) { // función para editar el tipo
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteHerramienta () { // función para eliminar un tipo
        var id = this.deleteItem.id // id del objeto a eliminar
        console.log(this.items.length)
        axios.delete(config.API_LOCATION + '/bodega/item/' + id + '') // petición DELETE a tipo para poder eliminar un tipo
          .then(response => {
            this.items.splice(this.deleteIndex, 1)
            console.log(this.items.length)
            this.dialogDelete = false// cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      close () {
        this.dialog = false
      },
      marcaClose () {
        this.dialog3 = false
        this.$refs.fagregarMarca.reset()
      },
      clearAddModal () {
        this.$refs.fAgregarHerramientas.reset()
        this.dialogAdd = false
        this.dialog3 = false
        this.selectValidado = false
        this.selectValidado2 = false
        this.selectValidado3 = false
      },
      clear () {
        // this.$refs.fAgregarCategoriaH.reset()
        this.dialogAdd = true
        this.dialog3 = false
      },
      clearEditModal () {
        this.dialogEdit = false
      },
      modalDetalle (item) {
        this.detailItem = this.items.indexOf(item) // obtener posición del array
        this.detailItem = Object.assign({}, item)
        this.dialogDetail = true
      },
      cerrarModalDetail () {
        this.dialogDetail = false
      }
    }
  }
</script>
