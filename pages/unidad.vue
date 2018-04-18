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
    <!-- Dialog Eliminar Unidad -->
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
            <v-card-text class="px-0" v-model="deleteItem.medida" style="text-align:center;">medida : {{deleteItem.medida}}</v-card-text>
          </v-card>
        </v-flex>

            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialogDelete = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="deleteUnidad">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Unidad-->
  <!-- Dialog Agregar Unidad -->
    <v-dialog v-model="dialogAdd" max-width="500px">
      <v-btn dark color="primary"  slot="activator" >Agregar Unidad</v-btn>
            <v-form @submit.prevent="agregarUnidad" v-model="valid" ref="fAgregarUnidad" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Unidad</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="medida"  :rules="textoRules" :counter="20" name="medidaUnidad" v-model="addItem.medida" required></v-text-field>
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
    <!-- Fin Dialog Agregar Unidad -->
    <!-- Dialog Editar Unidad -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editUnidad" ref="fEditarUnidad">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Unidad</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="medida" v-model="editedItem.id" name="idEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>

                <v-text-field label="medida" :counter="20" :rules="textoRules" v-model="editedItem.medida" name="medidaEdit"></v-text-field>
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
    <!-- Fin Dialog Editar Unidad -->
    <!-- Dialog Detalle Unidad -->
       <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="editUnidad">
      <v-card>
        <v-card-title>
          <span class="headline">Detalle Unidad</span>
        </v-card-title>
        <v-list dense >
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Unidad</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.medida }}</v-list-tile-title>
            </v-list-tile>

           </v-list>
           
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
    </v-dialog>
    <!-- Fin Dialog Editar Unidad -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>LISTA UNIDAD</h1>
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
        <td class="text-xs-center">{{ props.item.medida }}</td>
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
          <!--Boton Eliminar
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
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Unidad
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Unidad
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Unidad
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Unidad
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
        { text: 'medida', value: 'medida', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
      items: [],
      valid: true,
      addItem: {
        id: 0,
        medida: ''
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        medida: ''
      },
      detailItem: {
        id: 0,
        medida: ''
      },
      deleteItem: {
        id: 0,
        medida: ''
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
        axios.get(config.API_LOCATION + `/bodega/unidad_medida/`) // petición GET a Unidad para traer a todos los objetos "Unidad"
          .then((response) => {
            for (var i = response.data.length; i >= 0; i--) {
              this.items.push({id: '' + response.data[i - 1].id + '', medida: '' + response.data[i - 1].medida + ''})// LLenado del array "items"
            }
          })
          .catch(e => {
          })
      },
      agregarUnidad (e) { // función para agregar un nuevo Unidad
        var Unidad = e.target.elements.medidaUnidad.value
        if (this.$refs.fAgregarUnidad.validate()) {
          axios.post(config.API_LOCATION + '/bodega/unidad_medida/', { // petición POST a Unidad para agregar
            medida: '' + Unidad + ''
          })
            .then((response) => {
              this.items.push(response.data) // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.$refs.fAgregarUnidad.reset()
              this.snackbar = true
            })
        }
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      editUnidad (e) { // función para editar el Unidad
        var id = e.target.elements.idEdit.value // obtener id del objeto que se desea editar formulario
        var medida = e.target.elements.medidaEdit.value // obtener medida del objeto que se desea editar formulario
        if (this.$refs.fEditarUnidad.validate()) {
          axios.put(config.API_LOCATION + '/bodega/unidad_medida/' + id + '', {// petición put para editar el Unidad
            medida: '' + medida + ''
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
      modalDelete (item) { // función para editar el Unidad
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteUnidad () { // función para eliminar un Unidad
        var id = this.deleteItem.id // id del objeto a eliminar
        console.log(this.items.length)
        axios.delete(config.API_LOCATION + '/bodega/unidad_medida/' + id + '') // petición DELETE a Unidad para poder eliminar un Unidad
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
        this.$refs.fAgregarUnidad.reset()
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
