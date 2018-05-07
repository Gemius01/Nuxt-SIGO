<template>
	<div>
		 <!-- Dialog Agregar Modulo -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Modulo</v-btn>
            <v-form @submit.prevent="agregarModulo" v-model="valid" ref="fAgregarModulo" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Nuevo Modulo</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	<v-flex xs12>
            		<v-text-field label="Nombre" :counter="20" :rules="textoRules" id="nombreModulo" v-model="addItem.nombre" required></v-text-field>
            	</v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" @click="clearAddModal" flat>Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat :disabled="false" id="buttonAgregar">Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Agregar Modulo -->
    <!-- Dialog Editar Modulo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editarModulo" ref="fEditarModulo">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Modulo</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
							<v-flex xs12 sm6 md4 style="display:none;">
								<v-text-field label="Nombre" v-model="editedItem.id" name="idEdit"></v-text-field>
							</v-flex>
            	<v-flex xs12>
            		<v-text-field label="Nombre" :rules="textoRules" v-model="editedItem.nombre" id="nombreModuloEdit" :counter="20"></v-text-field>
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
    <!-- Fin Dialog Editar Modulo -->
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

     <!-- Dialog Editar Modulo -->
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
    <!-- Fin Dialog Editar Modulo -->

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
import validaciones from '../../validaciones.vue'
	export default {
    components: { config },
    layout: 'usuarioTemplate',
		data: () => ({
			headers: [
		        { text: 'ID', value: 'rut', sortable: true, width: '33%', align: 'center' },
		        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
		    ],
			textoRules: validaciones.textoRules,
			modulos: [],
			pagination: {},
			search: '',
			valid: true,
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
      dialogDelete: false,
			editedIndex: -1,
			addItem: {
        id: 0,
        nombre: ''
      },
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
		computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      }
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/modulo/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.modulos = response.data
          })
          .catch(e => {
          })
      },
			agregarModulo (e) {
        var nombre = e.target.elements.nombreModulo.value
				if (this.$refs.fAgregarModulo.validate()) {
					document.getElementById("buttonAgregar").disabled = true
					axios.post(config.API_LOCATION + '/user/modulo/', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
	          .then((response) => {
	            this.initialize()
							document.getElementById("buttonAgregar").disabled = false
							this.dialogAdd = false
							this.$refs.fAgregarModulo.reset()
	          })
	          .catch(e => {
	          })
				}
			},
			editarModulo (e) {
        var id = e.target.elements.idEdit.value
        var nombre = e.target.elements.nombreModuloEdit.value
				if (this.$refs.fEditarModulo.validate()) {
					axios.put(config.API_LOCATION + '/user/modulo/'+ id +'', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
	          .then((response) => {
	            this.dialogEdit = false
							Object.assign(this.modulos[this.editedIndex], this.editedItem)
	          })
	          .catch(e => {
	          })
				}
			},
      eliminarModulo (e) {
        axios.delete(config.API_LOCATION + '/user/modulo/' + this.deleteItem.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
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
				this.editedIndex = this.modulos.indexOf(item)
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
        this.$refs.fAgregarModulo.reset()
        this.dialogAdd = false
      },
		}
	}
</script>
