<template>
	<div>
		 <!-- Dialog Agregar Modulo -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Servicio</v-btn>
            <v-form @submit.prevent="agregarFuncion" v-model="valid" ref="fAgregarHerramientas" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Nuevo Servicio</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	<v-flex xs6>
            		<v-text-field label="Nombre" :counter="20" id="nombreServicio" v-model="addItem.nombre"></v-text-field>
            	</v-flex>
              <v-flex xs6>
                <v-text-field label="Precio" :counter="20" id="valorServicio" v-model="addItem.precio"></v-text-field>
              </v-flex>
              <v-flex>
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
              <td class="text-xs-center">{{ props.item.id_funcion }}</td>
              <td class="text-xs-center">{{ props.item.id_servicio }}</td>
              <td class="text-xs-center">{{ props.item.limit_reg }}</td>
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
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat>Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Agregar Modulo -->
      <v-dialog v-model="agregarItem" max-width="500px">
      <v-form @submit.prevent="agregarItemASalir" v-model="valid" ref="fagregarItem" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Agregar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm4>
                <h4>Servicio</h4>
                <v-select item-text="nombre" :items="servicioItem" v-model="addDetailServicio.id_servicio" single-line 
                ></v-select>
                </v-flex>

              <v-flex xs12 sm4>
                 <h4>Función</h4>
                <v-select item-text="nombre" :items="funcionItem" v-model="addDetailServicio.id_funcion"single-line 
                ></v-select>
                </v-flex>
                 <v-flex xs4>
                  <h4>Limite de Registro</h4>
                <v-text-field :counter="20" id="limiteServicio" v-model="addDetailServicio.limit_reg"></v-text-field>
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

    <!-- Dialog Editar Modulo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editarModulo" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Modulo</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	
              <v-layout wrap>
              
             
            </v-layout>
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

          <v-card-title><h1> Detalle de Servicio</h1></v-card-title>
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
          <span class="headline">¿Estás seguro de eliminar este Servicio?</span>
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
        <h1>Servicio</h1>
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
      :items="servicios"
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
        headersItem: [ // Encabezados de  la tabla
        { text: 'Funcion', value: 'id_funcion', width: '33%', align: 'center' },
        { text: 'Servicio', value: 'id_servicio', width: '33%', align: 'center' },
        { text: 'Limite de Servicio', value: 'limit_reg', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
			servicios: [],
			pagination: {},
			search: '',
			valid: true,
			dialogAdd: false,
      itemsASalir: [],
			dialogEdit: false,
			dialogDetail: false,
      agregarItem: false,
      funcionItem: [],
      servicioItem: [],
      paginationItems: {},
      dialogDelete: false,
      addItem: { // prop temporal que guarda el objeto a editar o eliminar
          id: 0,
          nombre: '',
          precio: 0
        },
      addDetailServicio: {
          id: 0,
          id_funcion: {
            id: 0,
            nombre: '',
            id_modulo: {
              id: 0,
              nombre: ''
            },
            acceso: '',
            mantencion: ''
          },
          limit_reg: 0,
          id_servicio: {
            id: 0,
            nombre: '',
            precio: 0
          }
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
      this.cargarSelectFuncion()
      this.cargarSelectServicio()
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/servicio/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.servicios = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectFuncion () {
        axios.get(config.API_LOCATION +  '/user/funcion/')// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.funcionItem = response.data
          })
          .catch(e => {
          })
      },
       agregarItemModal () {
        this.agregarItem = true
      },
      clearAgregarItemModal () {
        this.agregarItem = false
        this.$refs.fagregarItem.reset()
      },
      cargarSelectServicio () {
        axios.get(config.API_LOCATION +  '/user/servicio/')// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.servicioItem = response.data
          })
          .catch(e => {
          })
      },
			agregarFuncion (e) {
        var nombre = this.addItem.nombre
        var precio = this.addItem.precio

        axios.post(config.API_LOCATION + '/user/servicio/', 
        { nombre: nombre,
          precio: precio }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogAdd = false
          })
          .catch(e => {
          })
			},
        agregarItemASalir () {
        var limite = this.addDetailServicio.limit_reg
        console.log(limite)
        var funcion = this.addDetailServicio.id_funcion.nombre
        console.log(funcion)
        var servicio = this.addDetailServicio.id_servicio.nombre
        console.log(servicio)
        this.itemsASalir.push({limit_reg: limite, id_funcion: funcion, id_servicio: servicio})
        this.clearAgregarItemModal() 


      },  

			editarModulo (e) {
				alert(this.editedItem.id)
        var id = this.editedItem.id
        var nombre = e.target.elements.nombreModuloEdit.value
        axios.put(config.API_LOCATION + '/user/modulo/'+ id +'', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogEdit = false
          })
          .catch(e => {
          })
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