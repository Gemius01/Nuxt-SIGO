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
              <td class="text-xs-center">{{ props.item.id_funcion.nombre }}</td>
              <td class="text-xs-center">{{ props.item.id_servicio.nombre }}</td>
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
                <v-select  item-value="id" item-text="nombre" :items="servicioItem" v-model="addDetailServicio.id_servicio" single-line 
                ></v-select>
                </v-flex>

              <v-flex xs12 sm4>
                 <h4>Función</h4>
                <v-select item-value="id" item-text="nombre" :items="funcionItem" v-model="addDetailServicio.id_funcion"single-line 
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
        <v-form @submit.prevent="editarService" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Modulo</span>
        </v-card-title>
       <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field label="Nombre" :counter="20" id="nombreServicio" v-model="editedItem.nombre"></v-text-field>
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
  <v-dialog v-model="editarItemModal" max-width="500px">
      <v-form @submit.prevent="editarItemASalir" v-model="valid" ref="fEditarItemModal" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Editar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm4>
                <h4>Servicio</h4>
                <v-select item-value="id" item-text="nombre" :items="servicioItem" v-model="editItemSalir.id_servicio.id" single-line 
                ></v-select>
                </v-flex>
              <v-flex xs12 sm4>
                 <h4>Función</h4>
                <v-select item-value="id" item-text="nombre" :items="funcionItem" v-model="editItemSalir.id_funcion.id"single-line 
                ></v-select>
                </v-flex>
                 <v-flex xs4>
                  <h4>Limite de Registro</h4>
                <v-text-field :counter="20" id="limiteServicio" v-model="editItemSalir.limit_reg"></v-text-field>
              </v-flex>
         
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="clearEditarItemModal">Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
    </v-dialog>
        <v-dialog v-model="detalleItemModal" max-width="500px">
      <v-card>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
              <v-card>
              <v-card-title><h1> Detalle de Insumo</h1></v-card-title>
              <v-divider></v-divider>
              <v-list dense>
              <v-flex xs12>
              <v-expansion-panel focusable>
            <v-expansion-panel-content  v-for="(Item,i) in 1" :key="i">
              <div slot="header"><h4>Información del Servicio</h4></div>
              <v-list dense>

                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Id Servicio</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailService.id }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Nombre</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailService.nombre }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Id Cotización</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailService.precio }}</v-list-tile-title>
                  </v-list-tile>
      </v-list>
            </v-expansion-panel-content>
          </v-expansion-panel>
              </v-flex>
              <v-flex xs12>
              <v-expansion-panel focusable>
            <v-expansion-panel-content  v-for="(Item,i) in 1" :key="i">
              <div slot="header"><h4>Información del Función</h4></div>
              <v-list dense>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Id Función</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.id }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Nombre</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.nombre }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Nombre Módulo</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.id_modulo.nombre }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Acceso</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.acceso }}</v-list-tile-title>
                  </v-list-tile>
              <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Mantención</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.mantencion }}</v-list-tile-title>
                  </v-list-tile>
                  <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Tabla</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ detailFuntion.tabla_main }}</v-list-tile-title>
                  </v-list-tile>
                </v-list>
            </v-expansion-panel-content>
          </v-expansion-panel>
              </v-flex>
               <v-list-tile class="hoverMouse">
                    <v-list-tile-title>Limite de Servicio</v-list-tile-title>
                    <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                    <v-list-tile-title>{{ limit_reg }}</v-list-tile-title>
                  </v-list-tile>
            
              </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="detalleItemModal = false">Cerrar</v-btn>
      </v-card-actions>
      </v-card>
    </v-dialog>
     <v-dialog v-model="eliminarItemModalTable" max-width="500px">
      <v-form @submit.prevent="eliminarItem" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">¿Estás seguro de quitar el giro?</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
               <v-card>
                <v-list dense >

                     <v-list-tile class="hoverMouse" xs12>
              <v-list-tile-title>Servicio</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{deleteService.id_servicio.nombre }}</v-list-tile-title>
                  </v-list-tile>
                   <v-list-tile class="hoverMouse" xs12>
              <v-list-tile-title>Funcion</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{deleteService.id_funcion.nombre }}</v-list-tile-title>
                  </v-list-tile>
                   <v-list-tile class="hoverMouse" xs12>
              <v-list-tile-title>Limite de Servicio</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{deleteService.limit_reg }}</v-list-tile-title>
                  </v-list-tile>

                </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="eliminarItemModalTable = false">Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Quitar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
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
      eliminarItemModalTable: false,
      itemsASalir: [],
			dialogEdit: false,
			dialogDetail: false,
      agregarItem: false,
      editarItemModal: false,
      funcionItem: [],
      servicioItem: [],
      paginationItems: {},
      dialogDelete: false,
      detalleItemModal: false,
      editItemSalir: {
          limit_reg: 0, 
          id_funcion: {
            id: 0,
            nombre: '',
            precio: 0
          },
          id_servicio: {
            id: 0,
            nombre: '',
            precio: 0
          }
        },
      addItem: { // prop temporal que guarda el objeto a editar o eliminar
          id: '',
          nombre: '',
          precio: ''
        },
      service: {
        id_servicio: {
            id: '',
            nombre: '',
            precio: ''
          }
      },
      funtion: {
            id: 0,
            nombre: '',
            precio: 0
      },
      detailService:{
            id: 0,
            nombre: '',
            precio: 0
      },
      detailFuntion: {
            id: 0,
            nombre: '',
            id_modulo: {
              id: 0,
              nombre: ''
            },
            acceso: '',
            mantencion: '',
            tabla_main: ''
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
        limit_reg: 0,
        detailServicio: {
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
        deleteService: {
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
      eliminarItem (item) {
        
        var objFuncion = this.deleteService.id_funcion
        var objServicio = this.deleteService.id_servicio
        console.log('objServicio')
        console.log(objServicio)
        console.log('objFuncion')
        console.log(objFuncion)
        var indexArray = this.itemsASalir.findIndex(x => x === this.deleteService)
        this.itemsASalir.splice(indexArray, 1)

        this.servicioItem.push(this.deleteService.id_servicio)
        this.funcionItem.push(this.deleteService.id_funcion)

        this.text = 'Se ha quitado correctamente'
        this.snackbar = true
        this.eliminarItemModalTable = false

        //var item = this.deleteItem
        //console.log(item)
        /*
        var indexArray = this.itemsASalir.findIndex(x => x.item === item.item)
        console.log(indexArray)
        this.itemsASalir.splice(indexArray, 1)
        //this.itemsSelectSalida.push(item.item)
        this.text = 'Se ha quitado correctamente'
        this.snackbar = true
        this.eliminarItemModalTable = false
        */
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
           for (var i = 0; i < this.itemsASalir.length; i++) {
                  var limite = this.itemsASalir[i].limit_reg
                  var servicio = this.itemsASalir[i].id_servicio.id
                  var funcion = this.itemsASalir[i].id_funcion.id
                  axios.post(config.API_LOCATION + '/user/d_servicio/', {
                    limit_reg: limite, id_funcion: {id: funcion}, id_servicio: {id: servicio}
                  })
                }
			},
        agregarItemASalir () {
        var limite = this.addDetailServicio.limit_reg
        axios.get(config.API_LOCATION + '/user/funcion/' + this.addDetailServicio.id_funcion + '')
          .then((response) => {
            this.funtion = response.data
            var indexArrayFunction  = this.funcionItem.findIndex(x => x === this.funtion)
            this.funcionItem.splice(indexArrayFunction, 1)

            axios.get(config.API_LOCATION + '/user/servicio/' + this.addDetailServicio.id_servicio + '')
          .then((response) => {
            this.service.id_servicio = response.data
            var indexArrayService = this.servicioItem.findIndex(x => x === this.service.id_servicio)
            this.servicioItem.splice(indexArrayService, 1)
            console.log(this.funtion)
            this.itemsASalir.push({id_funcion: this.funtion, id_servicio: this.service.id_servicio, limit_reg: limite})
          })
          .catch(e => {
          })

          })
          .catch(e => {
          })
          this.agregarItem = false
      },  

			editarService (e) {
				alert(this.editedItem.id)
        var id = this.editedItem.id
        var nombre = this.editedItem.nombre
        axios.put(config.API_LOCATION + '/user/servicio/'+ id +'', { nombre: nombre }) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogEdit = false
          })
          .catch(e => {
          })
			},
      detalleItem (obj) {   
        this.detalleItemModal = true
        this.limit_reg = obj.limit_reg
         axios.get(config.API_LOCATION + '/user/funcion/' + obj.id_funcion.id + '')
          .then((response) => {
            this.detailFuntion = response.data
          })
          
        axios.get(config.API_LOCATION + '/user/servicio/' + obj.id_servicio.id + '')
          .then((response) => {
          this.detailService = response.data


          })
        .catch(e => {
        })
        .catch(e => {
          })
        this.clearAgregarItemModal()
      },
      eliminarModulo (e) {
        axios.delete(config.API_LOCATION + '/user/servicio/' + this.deleteItem.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
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
      editItemModal (item) {
        this.editItemSalir = item
        this.editarItemModal = true
        
      },
       eliminarItemModal (item) {
        this.deleteService = item
        this.eliminarItemModalTable = true
      },
			modalEdit (item) {
				this.editedItem = item
				this.dialogEdit = true
			},
       clearEditarItemModal () {
        this.editarItemModal = false
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