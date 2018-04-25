<template>
	<div>
		 <!-- Dialog Agregar Funcion -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Funcion</v-btn>
            <v-form @submit.prevent="agregarFuncion" v-model="valid" ref="fAgregarHerramientas" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Nuevo Funcion</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	<v-flex xs12>
            		<v-text-field label="Nombre" :counter="20" id="nombreModulo"></v-text-field>
            	</v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat>Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Agregar Funcion -->
    <!-- Dialog Editar Funcion -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editarFuncion" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Funcion</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	<v-flex xs12>
            		<v-text-field label="Nombre" v-model="editedItem.nombre" id="nombreModuloEdit" :counter="20"></v-text-field>
            	</v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalEdit">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat>Guardar</v-btn>
        </v-card-actions>
      </v-card>
    </v-form>
    </v-dialog>
    <!-- Fin Dialog Editar Funcion -->
     <!-- Dialog Detalle Funcion -->
        <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Funcion</h1></v-card-title>
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
          </v-list>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Detalle Funcion -->

     <!-- Dialog Editar Funcion -->
       <v-dialog v-model="dialogDelete" max-width="500px">
        <v-form @submit.prevent="eliminarFuncion" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">¿Estás seguro de eliminar este módulo?</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
              <v-list dense >
                <v-list-tile class="hoverMouse">
                <v-list-tile-title>ID</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.id }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Nombre</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.nombre }}</v-list-tile-title>
              </v-list-tile>
            </v-list>
          </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDelete">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat>Eliminar</v-btn>
        </v-card-actions>
      </v-card>
    </v-form>
    </v-dialog>
    <!-- Fin Dialog Editar Funcion -->

	<!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Modulos</h1>
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
      :items="modulos"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
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
          <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDelete(props.item)" >
            <v-icon color="red">delete</v-icon>
          </v-btn>
          <span>Eliminar</span>
          </v-tooltip>
        </td>
      </template>

      <template slot="pageText" slot-scope="{ pageStart, pageStop }">
        De {{ pageStart }} a {{ pageStop }}
      </template>
    </v-data-table>
    </v-card>
<!-- Fin Tabla-->


	</div>
</template>
<script>
import axios from 'axios'
import config from '../../config.vue'
	export default {
    components: { config },
    layout: 'usuarioTemplate',
		data: () => ({
			headers: [
		        { text: 'ID', value: 'rut', sortable: true, width: '33%', align: 'center' },
		        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
		    ],
			modulos: [],
			pagination: {},
			search: '',
			valid: true,
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
      dialogDelete: false,
			editedItem: { // prop temporal que guarda el objeto a editar o eliminar
	        id: 0,
	        nombre: ''
		    },
		    detailItem: { // prop temporal que guarda el objeto a mostrar en detalle
		      id: 0,
		      nombre: ''
		    },
        deleteItem: { // prop temporal que guarda el objeto a mostrar en detalle
          id: 0,
          nombre: ''
        }
		}),
    created () {
      this.initialize()
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/funcion/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.modulos = response.data
          })
          .catch(e => {
          })
      },
			agregarFuncion (e) {
        var nombre = e.target.elements.nombreModulo.value
        axios.post(config.API_LOCATION + '/user/funcion/', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogAdd = false
          })
          .catch(e => {
          })
			},
			editarFuncion (e) {
				alert(this.editedItem.id)
        var id = this.editedItem.id
        var nombre = e.target.elements.nombreModuloEdit.value
        axios.put(config.API_LOCATION + '/user/funcion/'+ id +'', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogEdit = false
          })
          .catch(e => {
          })
			},
      eliminarFuncion (e) {
        axios.delete(config.API_LOCATION + '/user/funcion/' + this.deleteItem.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogDelete = false
          })
          .catch(e => {
          })
      },
      modalDelete (item) {
        this.deleteItem = item
        this.dialogDelete = true
      },
			modalEdit (item) {
				this.editedItem = item
				this.dialogEdit = true
			},
			modalDetalle (item) {
				this.detailItem = item
				this.dialogDetail = true
			},
      cerrarModalDelete () {
        this.dialogDelete =  false
      },
			cerrarModalDetail () {
				this.dialogDetail =  false
			},
			cerrarModalEdit () {
				this.dialogEdit = false
			}
		}
	}
</script>