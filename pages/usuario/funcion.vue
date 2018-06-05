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
            		<v-text-field label="Nombre Función" :counter="20" id="nombreFuncion"></v-text-field>
            	</v-flex>
              <v-select 
                id='idselectModulo'
                item-value="id" 
                item-text="nombre" 
                :items="modulos"
                @select='onChangeSelect1' 
                v-model="selectModulo"
                search-input autocomplete label="Modulo"
                single-line
                ></v-select>
              <v-flex xs12>
                <v-select label="Nombre Seccion" :items="seccion" v-model="addItem.id_seccion" item-id="id" item-text="nombre" id="nombreSeccion"></v-select>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="URL" :counter="20" id="url" placeholder="/modulo/ejemplo"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-select label="Tabla Principal" :items="tablas" item-id="tabla_main" item-text="tabla_main" v-model="selectTabla" id="tablaPrincipal"></v-select>
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
                <v-text-field label="Nombre Función" v-model="editedItem.nombre" :counter="20" id="nombreFuncion"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-select item-value="id" item-text="nombre" :items="modulos" v-model="editedItem.id_modulo.id" label="Nombre Módulo" id="nombreModuloEdit"></v-select>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="URL" :counter="20" id="url" v-model="editedItem.acceso" placeholder="/modulo/ejemplo"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-select item-id="tabla_main" item-value="tabla_main" item-text="tabla_main" :items="tablas" v-model="editedItem.tabla_main" label="Tabla Principal" id="tablaPrincipalEdit"></v-select>
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
             <v-list-tile class="hoverMouse">
                <v-list-tile-title>Módulo</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.id_modulo.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Acceso</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.acceso }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Tabla Main</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.tabla_main }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Mantención</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.mantencion }}</v-list-tile-title>
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
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Módulo</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.id_modulo.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Acceso</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.acceso }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Tabla Main</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.tabla_main }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Mantención</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ deleteItem.mantencion }}</v-list-tile-title>
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
     <!-- Dialog Detalle Funcion -->
        <v-dialog v-model="dialogMantencion" max-width="500px">
        <form @submit.prevent="ponerEnMantencion">
      <v-card>

          <v-card-title><h1>Mantención Item</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ mantencionItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ mantencionItem.nombre }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
                <v-list-tile-title>Módulo</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ mantencionItem.id_modulo.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Acceso</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ mantencionItem.acceso }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Tabla Main</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ mantencionItem.tabla_main }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Mantención</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ mantencionItem.mantencion }}</v-list-tile-title>
              </v-list-tile>
          </v-list>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalMantencion">Cerrar</v-btn>
          <v-btn color="blue darken-1" flat type="submit">Poner en Mantención</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Detalle Funcion -->

	<!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Función</h1>
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
      :items="funciones"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.nombre }}</td>
        <td class="text-xs-center">{{ props.item.id_seccion.id_modulo.nombre }}</td>
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
          <v-btn icon slot="activator" class="mx-0" @click="modalMantencion(props.item)" >
            <v-icon color="orange darken-1">build</v-icon>
          </v-btn>
          <span>Mantención</span>
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
		        { text: 'ID', value: 'rut', sortable: true, width: '25%', align: 'center' },
		        { text: 'Nombre', value: 'nombre', width: '25%', align: 'center' },
            { text: 'Módulo', value: 'modulo', width: '25%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
		    ],
      funciones: [],
			modulos: [],
      seccion: [],
      tablas: [{tabla_main: 'Funcionario'}],
			pagination: {},
			search: '',
			valid: true,
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
      dialogDelete: false,
      dialogMantencion: false,
      selectModulo: 0,
      selectModuloEdit: {id: 0, nombre: ''},
      selectTablaEdit: 0,
      selectTabla: '',
      // SnackBar
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
        addItem: { // prop temporal que guarda el objeto a editar o eliminar
          id: 0,
          nombre: '',
          id_seccion: {id: 0, nombre: ''},
          mantencion: false,
          tabla_main: '',
          acceso: ''
        },
			editedItem: { // prop temporal que guarda el objeto a editar o eliminar
	        id: 0,
          nombre: '',
          id_modulo: {id: 0, nombre: ''},
          mantencion: false,
          tabla_main: '',
          acceso: ''
		    },
		    detailItem: { // prop temporal que guarda el objeto a mostrar en detalle
		      id: 0,
		      nombre: '',
          id_modulo: {id: 0, nombre: ''},
          mantencion: false,
          tabla_main: '',
          acceso: ''
		    },
        deleteItem: { // prop temporal que guarda el objeto a mostrar en detalle
          id: 0,
          nombre: '',
          id_modulo: {id: 0, nombre: ''},
          mantencion: false,
          tabla_main: '',
          acceso: ''
        },
        mantencionItem: {
          id: 0,
          nombre: '',
          id_modulo: {id: 0, nombre: ''},
          mantencion: false,
          tabla_main: '',
          acceso: ''
        }
		}),
    created () {
      this.initialize()
      this.cargarModulos()
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/funcion/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.funciones = response.data
          })
          .catch(e => {
          })
      },
      onChangeSelect1 (val) {
         var idmodulo = this.selectModulo
         axios.get(config.API_LOCATION +  '/user/seccion/' + idmodulo + '/modulo')// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            console.log(response.data)
            this.seccion = response.data
            

          })
          .catch(e => {
          })
      },
      cargarModulos () {
        axios.get(config.API_LOCATION + '/user/modulo/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.modulos = response.data
          })
          .catch(e => {
          })
      },
			agregarFuncion (e) {
        var funcion = e.target.elements.nombreFuncion.value
        var seccion = this.addItem.id_seccion.id
        console.log(seccion)
        var url = e.target.elements.url.value
        var tablaPrincipal = this.selectTabla.tabla_main
        axios.post(config.API_LOCATION + '/user/funcion/', { 
          nombre: funcion,
          id_seccion: {id: seccion},
          tabla_main: tablaPrincipal,
          acceso: url,
          mantencion: false
         }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogAdd = false
            this.color = 'green'
            this.text = 'Se ha agregado una Función'
            this.snackbar = true
          })
          .catch(e => {
          })
			},
			editarFuncion (e) {
        axios.put(config.API_LOCATION + '/user/funcion/'+ this.editedItem.id +'', this.editedItem) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogEdit = false
            this.color = 'green'
            this.text = 'Se ha modificado una Función'
            this.snackbar = true
          })
          .catch(e => {
          })
			},
      ponerEnMantencion () {
        this.mantencionItem.mantencion = true
        axios.put(config.API_LOCATION + '/user/funcion/'+ this.mantencionItem.id +'', this.mantencionItem) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogMantencion = false
            this.color = 'green'
            this.text = 'Se ha modificado con éxito'
            this.snackbar = true
          })
          .catch(e => {
          })
      },
      eliminarFuncion (e) {
        axios.delete(config.API_LOCATION + '/user/funcion/' + this.deleteItem.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogDelete = false
            this.color = 'green'
            this.text = 'Se ha eliminado con éxito'
            this.snackbar = true
          })
          .catch(e => {
          })
      },
      modalDelete (item) {
        this.deleteItem = item
        this.dialogDelete = true
      },
			modalEdit (item) {
        this.selectModuloEdit = item.id_modulo
        this.selectTablaEdit = item.tabla_main
				this.editedItem = item
        console.log(this.editedItem)
				this.dialogEdit = true
			},
      modalMantencion(item) {
        this.mantencionItem = item
        this.dialogMantencion = true
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
      cerrarModalMantencion () {
        this.dialogMantencion = false
      },
			cerrarModalEdit () {
				this.dialogEdit = false
			}
		}
	}
</script>