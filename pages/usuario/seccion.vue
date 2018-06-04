<template>
	<div>
		<!-- Dialog Agregar Modulo -->
	 <v-dialog v-model="dialogAdd" max-width="500px" >
		 <v-btn dark color="primary"  slot="activator" >Agregar Sección</v-btn>
					 <v-form @submit.prevent="agregarSeccion" v-model="valid" ref="fAgregarSeccion" lazy-validation>
		 <v-card> <!-- PICO PAL QUE LEE-->
			 <v-card-title>
				 <span class="headline">Nueva Sección</span>
			 </v-card-title>
			 <v-card-text>
				 <v-container grid-list-md>
					 <v-layout wrap>
						 <v-flex xs12>
							 <v-text-field label="Nombre" :counter="20" :rules="textoRules" ref="txtNombre" id="nombreSeccion" v-model="addItem.nombre" required></v-text-field>
						 </v-flex>
						 <v-flex xs3>
								<v-subheader>Modulo : </v-subheader>
						 </v-flex>
						 <v-flex xs9>
						 <v-select
						 :items="modulo"
						 item-text="nombre"
						 item-value="id"
						 v-model="addItem.moduloVal"
						 search-input
						 v-on:change="onChangeSelectAgregar"
						 :rules="[v => this.selectValidado || 'Campo Vacío']"
						 required
						 autocomplete
						 label="Modulo"
						 single-line
						 ></v-select>
						 </v-flex>

					 </v-layout>
				 </v-container>
			 </v-card-text>
			 <v-card-actions>
				 <v-spacer></v-spacer>
				 <v-btn color="blue darken-1" @click="clearAddModal" flat>Cancelar</v-btn>
				 <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
			 </v-card-actions>
		 </v-card>
		 </v-form>
	 </v-dialog>
	 <!-- Fin Dialog Agregar Modulo -->
	 <!-- Dialog Editar Subcategoria -->
	 	 <v-dialog v-model="dialogEdit" max-width="500px">
	 		<v-form @submit.prevent="editSeccion" ref="fEditarSeccion">
	 	<v-card>
	 		<v-card-title>
	 			<span class="headline">Editar Seccion</span>
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
	 						 <v-subheader>Modulo : </v-subheader>
	 					</v-flex>
	 					<v-flex xs9>
	 			<v-select
	 				:items="modulo"
	 				item-text="nombre"
	 				item-value="id"
	 				@select='onChangeSelect'
	 				v-model="editedItem.id_modulo.id"
	 				:error-messages="errorMessages"
	 				search-input
	 				autocomplete
	 				label="Modulo"
	 				single-line
	 			></v-select>
	 		</v-flex>
	 				</v-layout>
	 			</v-container>
	 		</v-card-text>
	 		<v-card-actions>
	 			<v-spacer></v-spacer>
	 			<v-btn color="blue darken-1" flat @click.native="	cerrarModalEdit">Cancelar</v-btn>
	 			<v-btn color="blue darken-1" type="submit" flat>Guardar</v-btn>
	 		</v-card-actions>
	 	</v-card>
	 </v-form>
	 </v-dialog>
	 <!-- Fin Dialog Editar Subcategoria -->
     <!-- Dialog Detalle Modulo -->
        <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Modulo</h1></v-card-title>
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
    <!-- Fin Dialog Detalle Modulo -->

     <!-- Dialog Eliminar Modulo -->
       <v-dialog v-model="dialogDelete" max-width="500px">
        <v-form @submit.prevent="eliminarModulo" ref="fEditarHerramientas">
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
    <!-- Fin Dialog Eliminar Modulo -->

	<!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Sección</h1>
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
      :items="secciones"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.nombre }}</td>
				<td class="text-xs-center">{{ props.item.id_modulo.nombre }}</td>
			 <td class="text-xs-center" style="display:none;">{{ props.item.id_modulo.id }}</td>
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
import validaciones from '../../validaciones.vue'
	export default {
    components: { config },
    layout: 'usuarioTemplate',
		data: () => ({
			headers: [
		        { text: 'ID', value: 'rut', sortable: true, width: '33%', align: 'center' },
		        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
						{ text: 'Modulo', value: 'nombre', width: '33%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
		    ],
			textoRules: validaciones.textoRules,
			pagination: {},
			secciones: [],
			search: '',
			valid: true,
			selectValidado: false,
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
			snackbar: false,
			color: 'green',
			mode: '',
			timeout: 3000,
			text: 'Se ha agregado con exito',
      dialogDelete: false,
			editedIndex: -1,
			seccion: [],
			addItem: {
        id: 0,
        nombre: ''
      },
			editedItem: { // prop temporal que guarda el objeto a editar o eliminar
	        id: 0,
	        nombre: '',
					id_modulo: {id: 0}
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
			this.cargarSelectModulo()
    },
		computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      }
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/seccion/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.secciones = response.data
          })
          .catch(e => {
          })
      },
			onChangeSelect (val) {
			this.editedItem.modulo = val.target.value
		},
		onChangeSelectAgregar (val) {
			this.selectValidado = true
		},
		cargarSelectModulo () {
			axios.get(config.API_LOCATION + `/user/modulo/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
				.then((response) => {
					this.modulo = response.data
				})
				.catch(e => {
				})
		},
			agregarSeccion (e) {
	  		var nombre = this.addItem.nombre
				var moduloVal = this.addItem.moduloVal
					if (this.$refs.fAgregarSeccion.validate()) {
						axios.post(config.API_LOCATION + '/user/seccion/', {// petición GET a Tipo para traer a todos los objetos "tipo"
						nombre: '' + nombre + '',
						id_modulo : {id: moduloVal}
					})
		          .then((response) => {
								this.initialize()
	 						 this.dialogAdd = false // cerrar el modal
	 						 this.text = 'Se ha agregado correctamente'
	 						 this.snackbar = true
	 						 this.$refs.fAgregarSeccion.reset()
	 						 this.selectValidado = false
		          })
		          .catch(e => {
		          })
					}
				},
				editSeccion () { // función para editar la Subcategoría
					var id = this.editedItem.id // obtener id del objeto que se desea editar formulario
					var nombre = this.editedItem.nombre // obtener nombre del objeto que se desea editar formulario
					var idModulo = this.editedItem.id_modulo.id
					if (this.$refs.fEditarSeccion.validate()) {
						axios.put(config.API_LOCATION + '/user/seccion/' + id + '', {// petición put para editar el tipo
							nombre: '' + nombre + '', id_modulo: { id: idModulo }
						})
							.then(response => {
								this.initialize()
								this.text = 'Se ha modificado correctamente'
								this.snackbar = true
								this.dialogEdit = false // cerrar modal
							})
							.catch(function (error) {
								console.log(error)
							})
					}
				},

      eliminarModulo (e) {
        axios.delete(config.API_LOCATION + '/user/seccion/' + this.deleteItem.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
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
				this.editedIndex = this.secciones.indexOf(item)
				this.editedItem = Object.assign({}, item)
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
			},
			clearAddModal () {
					this.$refs.txtNombre.reset()
					this.dialogAdd = false
					this.modulo = []
	      },
		}
	}
</script>
