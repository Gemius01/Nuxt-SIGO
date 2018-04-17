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
          <v-btn color="blue" flat @click="deleteCategoria">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Tipo-->
  <!-- Dialog Agregar Tipo -->
    <v-dialog v-model="dialogAdd" max-width="500px">
      <v-btn dark color="primary"  slot="activator" >Agregar Categoria</v-btn>
            <v-form @submit.prevent="agregarCategoria" v-model="valid" ref="fAgregarCategoria" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Categoria</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Nombre" :rules="textoRules" :counter="20" name="nombreCategoria" v-model="addItem.nombre" required></v-text-field>
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
    <!-- Dialog Editar Tipo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editCategoria" ref="fEditarCategoria">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Categoria</span>
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
       
      </v-flex>               
      <v-card>
          <v-card-title><h1> Detalle de Categoría Insumos </h1></v-card-title>
          <v-divider></v-divider>

          <v-list dense >
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID Categoría</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Categoría</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.nombre }}</v-list-tile-title>
            </v-list-tile>
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
    <!-- Fin Dialog Editar Tipo -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>LISTA CATEGORÍA INSUMOS</h1>
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
  import validaciones from '../validaciones.vue'
  export default {
    components: { config },
    data: () => ({
      textoRules: validaciones.textoRules,
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Tipo
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Tipo
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Tipo
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Tipo
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
          width: '33%',
          align: 'center'
        },
        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
      items: [],
      valid: true,
      addItem: {
        id: 0,
        nombre: ''
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        nombre: ''
      },
      detailItem: {
        id: 0,
        nombre: ''
      },
      deleteItem: {
        id: 0,
        nombre: ''
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
      this.initialize()
    },
    methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/categoria/tipo/insumo`) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            console.log(response.data.length)
            for (var i = response.data.length; i >= 0; i--) {
              this.items.push({id: '' + response.data[i - 1].id + '', nombre: '' + response.data[i - 1].nombre + ''})// LLenado del array "items"
            }
          })
          .catch(e => {
          })
      },
      agregarCategoria (e) { // función para agregar un nuevo Tipo
        var categoria = e.target.elements.nombreCategoria.value
        var tipo = 2
        if (this.$refs.fAgregarCategoria.validate()) {
          axios.post(config.API_LOCATION + '/bodega/categoria/', { // petición POST a Tipo para agregar
            nombre: '' + categoria + '', id_tipo: { id: tipo }}
          )
            .then((response) => {
              this.items.push(response.data) // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.snackbar = true
            })
        }
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      editCategoria (e) { // función para editar el tipo
        var id = e.target.elements.idEdit.value // obtener id del objeto que se desea editar formulario
        var nombre = e.target.elements.nombreEdit.value // obtener nombre del objeto que se desea editar formulario
        var tipo = 2
        if (this.$refs.fEditarCategoria.validate()) {
          axios.put(config.API_LOCATION + '/bodega/categoria/' + id + '', {// petición put para editar el tipo
            nombre: '' + nombre + '', id_tipo: { id: tipo }
          })
            .then(response => {
              Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
              this.dialogEdit = false // cerrar modal
            })
            .catch(function (error) {
              console.log(error)
            })
        }
      },
      modalDelete (item) { // función para editar el tipo
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteCategoria () { // función para eliminar un tipo
        var id = this.deleteItem.id // id del objeto a eliminar
        console.log(this.items.length)
        axios.delete(config.API_LOCATION + '/bodega/categoria/' + id + '') // petición DELETE a tipo para poder eliminar un tipo
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
      clearAddModal () {
        this.$refs.fAgregarCategoria.reset()
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
