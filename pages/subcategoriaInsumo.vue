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
    <!-- Dialog Eliminar Subcategoría -->
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
        <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.categoria" style="text-align:center;">Categoría : {{deleteItem.categoria}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.id_categoria" style="text-align:center;">ID_Categoría : {{deleteItem.id_categoria}}</v-card-text>
          </v-card>
        </v-flex>

            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialogDelete = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="deleteSubCat">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Subcategoría-->
  <!-- Dialog Agregar Subcategoria -->
    <v-dialog v-model="dialogAdd" max-width="500px">
      <v-btn dark color="primary"  slot="activator" >Agregar Subcategoría</v-btn>
            <v-form @submit.prevent="agregarSubCat" v-model="valid" ref="fAgregarSubCat" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Subcategoría</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Nombre"  :rules="textoRules" :counter="20" name="nombreSubCat" v-model="addItem.nombre" required></v-text-field>
              </v-flex>
              <v-flex xs3>
                 <v-subheader>Categoría : </v-subheader>
              </v-flex>
              <v-flex xs9>
          <v-select
            id="selectCat"
            :items="categorias"
            item-text="nombre"
            value="id"
            v-model="categoriaSelectID"
            search-input
            :rules="[() => !!this.categoriaSelectID.id || 'This field is required']"
            :error-messages="errorMessages"
            required
            autocomplete
            label="Categoría"
            single-line
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
    <!-- Fin Dialog Agregar Subcategoria -->
    <!-- Dialog Editar Subcategoria -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <form @submit.prevent="editSubCat">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Subcategoria</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Nombre" v-model="editedItem.id" name="idEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Nombre" :counter="20" :rules="textoRules" v-model="editedItem.nombre" name="nombreEdit"></v-text-field>
              </v-flex>
               <v-flex xs3>
                 <v-subheader>Categoría : </v-subheader>
              </v-flex>
              <v-flex xs9>
          <v-select
            id="selectCatEdit"
            :items="categorias"
            item-text="nombre"
            item-value="id"
            @select='onChangeSelect'
            v-model="editedItem.id_categoria"
            :rules="[() => !!this.editedItem.id || 'This field is required']"
            :error-messages="errorMessages"
            search-input
            autocomplete
            label="Categoría"
            single-line
          ></v-select>
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
       </form>
    </v-dialog>
    <!-- Fin Dialog Editar Subcategoria -->
    <!-- Dialog Detalle Subcategoria -->
<v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Subcategoría Insumo</h1></v-card-title>
          <v-divider></v-divider>

          <v-list dense >
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID Subcategoría</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Subcategoría</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Categoria</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.categoria }}</v-list-tile-title>
            </v-list-tile>

           </v-list>
           

       
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
    </v-dialog>
    <!-- Fin Dialog Editar Subcategoria -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>SUBCATEGORÍA INSUMOS</h1>
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
        <td class="text-xs-center">{{ props.item.categoria }}</td>
        <td class="text-xs-center" style="display:none;">{{ props.item.id_categoria }}</td>
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
          <!-- boton eliminar
          <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDelete(props.item)">
            <v-icon color="red">delete</v-icon>
          </v-btn>
          <span>Eliminar</span>
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
  export default {
    components: { config },
    data: () => ({
      textoRules: [(v) => !!v || 'Campo vacío', v => (v && v.length <= 20) || 'Máximo de caracteres'],
      errorMessages: [],
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Subcategoría
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Subcategoría
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Subcategoría
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Subcategoría
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      categoriaSelectID: {},
      categoriaSelectIDEdit: {},
      value: '',
      search: '',
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      categorias: [],
      headers: [ // Encabezados de  la tabla
        {
          text: 'ID',
          value: 'id',
          sortable: true,
          width: '25%',
          align: 'center'
        },
        { text: 'Nombre', value: 'nombre', width: '25%', align: 'center' },
        { text: 'Categoría', value: 'categoria', width: '25%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
      ],
      items: [],
      valid: true,
      addItem: {
        id: 0,
        nombre: ''
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        nombre: '',
        categoria: '',
        id_categoria: 0
      },
      detailItem: {
        id: 0,
        nombre: '',
        categoria: '',
        id_categoria: 0
      },
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
      verVal (val) {
        console.log(val)
        console.log(this.value)
      }
    },
    created () {
      this.initialize()
      this.cargarSelectCat()
    },
    methods: {
      onChangeSelect (val) {
        this.editedItem.categoria = val.target.value
      },
      cargarSelectCat () {
        axios.get(config.API_LOCATION + `/bodega/categoria/tipo/insumo/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            for (var i = response.data.length; i >= 0; i--) {
              this.categorias.push({id: '' + response.data[i - 1].id + '', nombre: '' + response.data[i - 1].nombre + ''})// LLenado del array "items"
            }
          })
          .catch(e => {
          })
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/subcategoria/tipo/insumo/`) // petición GET a Subcategoría para traer todos los objetos "Subcategoría" que contengan como tipo insumo
          .then((response) => {
            for (var i = response.data.length; i >= 0; i--) {
              this.items.push({id: '' + response.data[i - 1].id + '', nombre: '' + response.data[i - 1].nombre + '', categoria: '' + response.data[i - 1].id_categoria.nombre + '', id_categoria: '' + response.data[i - 1].id_categoria.id + ''})// LLenado del array "items"
            }
          })
          .catch(e => {
          })
      },
      agregarSubCat (e) { // función para agregar un nuevo Subcategoría
        var subCategoria = e.target.elements.nombreSubCat.value
        var categoriaVal = this.categoriaSelectID.id
        var categoriaNombre = e.target.elements.selectCat.value
        console.log(categoriaNombre)
        axios.post(config.API_LOCATION + '/bodega/subcategoria/', { // petición POST a Subcategoría para agregar
          nombre: '' + subCategoria + '', id_categoria: { id: categoriaVal, nombre: '' + categoriaNombre + '' }
        })
          .then((response) => {
            console.log(response.data)
            this.items.push({id: '' + response.data.id + '', nombre: '' + response.data.nombre + '', categoria: '' + categoriaNombre + '', id_categoria: '' + response.data.id_categoria.id + ''})// LLenado del array "items"
            this.dialogAdd = false // cerrar el modal
            this.snackbar = true
          }
          )
      },
      editSubCat (e) { // función para editar la Subcategoría
        console.log(this.varPrueba)
        console.log(this.editedItem)
        var id = e.target.elements.idEdit.value // obtener id del objeto que se desea editar formulario
        var nombre = e.target.elements.nombreEdit.value // obtener nombre del objeto que se desea editar formulario
        var idCategoria = this.editedItem.id_categoria
        axios.put(config.API_LOCATION + '/bodega/subcategoria/' + id + '', {// petición put para editar el tipo
          nombre: '' + nombre + '', id_categoria: { id: idCategoria }
        })
          .then(response => {
            Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
            this.dialogEdit = false // cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      modalDelete (item) { // función abrir modal delete
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteSubCat () { // función para eliminar un Subcategoría
        var id = this.deleteItem.id // id del objeto a eliminar
        axios.delete(config.API_LOCATION + '/bodega/subcategoria/' + id + '') // petición DELETE a Subcategoría para poder eliminar una Subcategoría
          .then(response => {
            this.items.splice(this.deleteIndex, 1)
            this.dialogDelete = false// cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      close () {
        this.dialog = false
      },
      clearAddModal () {
        // this.$refs.fAgregarSubCat.reset()
        this.dialogAdd = false
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
<style type="text/css">
  .hoverMouse:hover{
    background-color: #D5CFCF;
  }
</style>
