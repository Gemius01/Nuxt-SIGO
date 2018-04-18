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
  <!-- Dialog  Realizar Orden de Compra -->
    <v-dialog v-model="dialogAdd"  max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Realizar Orden de Compra</v-btn>
       <v-form @submit.prevent="agregarOrdenCompra" lazy-validation>
        <v-card>
          <v-card-title>
            <span class="headline">Realizar Orden de Compra</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs12>
               <v-text-field label="Fecha" id="fecha" value="2017-03-22"  disabled></v-text-field> 
              </v-flex>
                <v-flex xs12>
                  <v-flex xs4
                      <v-checkbox
                      color="primary"
                  label="Cotizable"
                  v-model="checkbox"
                ></v-checkbox>
                </v-flex>
                </v-flex>
                <v-flex xs12>
              <v-data-table
              :headers="headersItem"
              :items="itemsAOrdenar"
              :search="search"
              must-sort
              :pagination.sync="paginationItems"
              class="elevation-1"
              >
            <!-- Fin Tabla-->
            <template slot="items" slot-scope="props">
              <td class="text-xs-center">{{ props.item.item.nombre }}</td>
              <td class="text-xs-center">{{ props.item.cantidad }}</td>
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
                  <v-btn icon slot="activator" class="mx-0" @click="eliminarItem(props.item)">
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
          <v-card-actions >
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" outline flat @click="clearAddModal">Cancelar</v-btn>
            <v-btn color="blue darken-1" outline type="submit" flat >Guardar</v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Realizar Orden de Compra -->
    <v-dialog v-model="agregarItem" max-width="500px">
      <v-form @submit.prevent="agregarItemAOrdenar" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Agregar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-select
              id="selectItemOrden"
              :items="itemsSelectOrden"
              item-text="nombre"
              :value="selectItem.id"
              v-model="selectItem"
              @change="changeSelectPrestamo"
              search-input
              :rules="[() => !!this.selectItem || 'Campo requerido']"
              :error-messages="errorMessages"
              required
              autocomplete
              label="Item"
              single-line
              ></v-select>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number" id="cantidad" v-model="addItem.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat >Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
    <!-- Dialog Asignar Cotización -->
        <v-dialog v-model="agregarItem" max-width="500px">
      <v-form @submit.prevent="agregarItemAOrdenar" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Agregar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-select
              id="selectItemOrdenEdit"
              :items="itemsSelectOrden"
              item-text="nombre"
              :value="selectItem.id"
              v-model="selectItem"
              @change="changeSelectPrestamo"
              search-input
              :rules="[() => !!this.selectItem || 'Campo requerido']"
              :error-messages="errorMessages"
              required
              autocomplete
              label="Item"
              single-line
              ></v-select>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number" id="cantidad" v-model="addItem.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat >Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
    <!-- Dialog Editar Item a Prestar -->
    <v-dialog v-model="editarItemModal" max-width="500px">
      <v-form @submit.prevent="editarItemAOrdenar" v-model="valid" ref="fagregarMarca" lazy-validation>
        <v-card>
        <v-card-title>
        <span class="headline">Editar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs6>
              <v-text-field label="Item" v-model="editItemOrden.item.nombre" required disabled></v-text-field>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Cantidad" type="number"  v-model="editItemOrden.cantidad" required></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat >Cancelar</v-btn>
        <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
<!-- Fin Dialog Editar Item a Prestar -->
<!-- Detalle Item a Prestar -->
    <v-dialog v-model="detalleItemModal" max-width="500px">
      <v-card>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
              <v-card>
              <v-card-title><h1> Detalle de Insumo</h1></v-card-title>
              <v-divider></v-divider>
              <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.id }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Marca</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.id_marca.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Modelo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.modelo }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock Critico</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.stockCritico }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.stock }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Subcategoria</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.id_subcategoria.nombre }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>Unidad de medida</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItemOrden.id_unidad_medida.medida }}</v-list-tile-title>
              </v-list-tile>
              </v-list>
              </v-card>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" flat @click="detalleItemModal = false">Cancelar</v-btn>
      </v-card-actions>
      </v-card>
    </v-dialog>

       <v-dialog v-model="dialogAsignar" max-width="500px">
        <form @submit.prevent="asignarOrdenCompra">
      <v-card>
        <v-card-title>
          <span class="headline">Asignar Cotización </span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex>
              <v-text-field label="Número Orden"   type="number" id="fecha" v-model="asignarItem.id"  disabled></v-text-field>
              </v-flex>
                <v-flex xs12 class="divItemCotizacion">
                <h4 class="textDivCotizacion"> Cotizaciones</h4>
                 <v-flex xs12 v-for="(cotizacion, key, index) in cotizacionesByCompra">
                   <v-container grid-list-md text-xs-center>
                    <v-layout row wrap v-if="cotizacion.incompleta == false" class="cotizacionCompleta">
                      <v-flex xs2>
                          <v-text-field  label="N°" :value="cotizacion.id"  disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4>
                       <v-text-field  :value="cotizacion.rut_proveedor.nombre" label="Proveedor"  disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4 >
                       <v-text-field  label="Total" :value="cotizacion.total_neto"  disabled></v-text-field> 
                      </v-flex>
                      <v-flex xs1>
                        <v-btn color="blue" class="btnOptions" @click="detalleItemCot(cotizacion)">
                          <v-icon dark>search</v-icon>
                        </v-btn>
                      </v-flex>
                      <v-flex xs1>
                         <v-checkbox
                         :cotizacion="cotizacion.id"
                         :name="checkBoxArray[key].name"
                         @change="clickCheckBox(checkBoxArray[key].checkboxBoolean, checkBoxArray[key].name)"
                         color="primary"
                          v-model="checkBoxArray[key].checkboxBoolean"
                        ></v-checkbox>
                      </v-flex>
                    </v-layout>
                     <v-layout row wrap v-if="cotizacion.incompleta == true" class="cotizacionIncompleta">
                      <v-flex xs2>
                          <v-text-field  label="N°" :value="cotizacion.id"  disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4>
                       <v-text-field  :value="cotizacion.rut_proveedor.nombre" label="Proveedor"  disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4 >
                       <v-text-field  label="Total" :value="cotizacion.total_neto"  disabled></v-text-field> 
                      </v-flex>
                      <v-flex xs1>
                        <v-btn color="blue" class="btnOptions" @click="detalleItemCot(cotizacion)">
                          <v-icon dark>search</v-icon>
                        </v-btn>
                      </v-flex>
                      <v-flex xs1>
                         <v-checkbox
                          color="primary"
                          
                          disabled
                          indeterminate
                        ></v-checkbox>
                      </v-flex>
                    </v-layout>
                  </v-container>
                 </v-flex>
                </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="clearEditModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat>Asignar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
    </v-dialog>
    <!-- Fin Dialog Asignar Cotizaciòn -->
    <!-- Dialog Detalle Orden de Compra -->
       <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>
  
          <v-card-title><h1> Detalle de Salida</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Cotizable</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.cotizable }}</v-list-tile-title>
            </v-list-tile>
            
            <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content v-for="item in this.itemsOrden">
        <div slot="header">Item : {{ item.id_item.nombre }}</div>
        <v-card>
          <v-card-text class="light-green lighten-3">Cantidad : {{ item.cantidad }}</v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
          </v-list>
 
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Detalle Orden de Compra -->
    <!-- Dialog Detalle Cotizacion -->
       <v-dialog v-model="cotizacionDetail" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Detalle Cotización</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="itemCotizacionDetail.id" style="text-align:center;">ID cotización : {{itemCotizacionDetail.id}}</v-card-text>
          </v-card>
        </v-flex>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="itemCotizacionDetail.fecha" style="text-align:center;">Fecha : {{itemCotizacionDetail.fecha}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="itemCotizacionDetail.id_orden_compra" style="text-align:center;">ID Orden Compra : {{itemCotizacionDetail.id_orden_compra.id}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="itemCotizacionDetail.id_orden_compra" style="text-align:center;">ID Orden Compra : {{itemCotizacionDetail.id_orden_compra.id}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="itemCotizacionDetail.total_neto" style="text-align:center;">Total Neto : {{itemCotizacionDetail.total_neto}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content v-for="item in this.detailItemsCotizacion">
        <div slot="header">Item : {{ item.id_item.nombre }}</div>
        <v-card>
          <v-card-text class="light-green lighten-3">Cantidad : {{ item.cantidad }}</v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetailCotizacion">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Fin Dialog Detalle Cotizacion -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Ordenes de Compra</h1>
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
        <td class="text-xs-center">{{ props.item.fecha }}</td>
        <td class="text-xs-center">
        <v-checkbox
              primary
              hide-details
              :input-value="props.item.cotizable"
              disabled
        ></v-checkbox>
        </td>
        <td v-if="props.item.cotizable  == true" class="justify-left layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
            <v-icon color="blue">search</v-icon>
          </v-btn>
          <span>Detalle</span>
          </v-tooltip>

         
        <v-tooltip top v-if="props.item.cotizable  == true">
          <v-btn icon slot="activator" class="mx-0" @click="modalAsignar(props.item)">
            <v-icon color="indigo darken-3">assignment</v-icon>
          </v-btn>
          <span>Asignar Cotización</span>
          </v-tooltip>
    
              
        </td>
        <td  v-if="props.item.cotizable  == false" class="justify-left layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
            <v-icon color="blue">search</v-icon>
          </v-btn>
          <span>Detalle</span>
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
  import item from '../components/orden_compra/item.vue'
  export default {
    components: { config, item },
    data: () => ({
      headers: [ // Encabezados de  la tabla
        { text: 'Fecha', value: 'fecha', width: '33%', align: 'center' },
        { text: 'Cotización', value: 'cotizable', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '10%', align: 'left' }
      ],
      headersItem: [ // Encabezados de  la tabla
        { text: 'Nombre', value: 'nombre', width: '33%', align: 'center' },
        { text: 'Cantidad', value: 'cantidad', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
      textoRules: config.rules,
      active: null,
      valid: true,
      checkbox: false,
      activeForm: 1,
      errorMessages: [],
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Orden de Compra
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Orden de Compra
      dialogAsignar: false, // prop para abrir y cerrar modal de Asignar Cotización
      cotizacionDetail: false, // prop para abrir y cerrar modal detalle cotizacion
      detalleItemModal: false,
      editarItemModal: false,
      editItemOrden: {item: {id: 1, nombre: 'asd', marca: ''}, cantidad: 0},
      agregarItem: false,
      itemsAOrdenar: [],
      itemsSelectOrden: [],
      paginationItems: {},
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      devolverIndex: -1,
      labelCantidad: 'Disponible : ' + 0 + '',
      value: '',
      selectItem: 0,
      search: '',
      checkBoxArray: [], // Array boolean que estarán dentro de la iteraciones de cotización en el modal asignar cotizacion
      inputArray: [],
      // Props Snack Bar
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      // Fin Props Snack Bar
      items: [],
      proveedores: [],
      ordenCompraSelected: [],
      cotizaciones: [],
      cotizacionesByCompra: [],
      itemsCotizacion: [],
      detailItemsCotizacion: [],
      selectProveedor: 0,
      selectCotizacion: 0,
      asignarItem: {
        id: 0
      },
      addItem: {
        id: 0,
        solicitante: '',
        motivo: '',
        cantidad: 0,
        itemID: '',
        stock: 0
      },
      detailItemOrden: {
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stockCritico: '',
        stock: ''
      },
      itemCotizacionDetail: {
        id: 0,
        fecha: '',
        rut_proveedor: {
          rut: '',
          nombre: '',
          representante: '',
          direccion: '',
          id_ciudad: {
            id: 0,
            nombre: '',
            id_region: {
              id: 0,
              nombre: '',
              id_pais: {
                id: 0,
                nombre: ''
              }
            }
          },
          fono: '',
          movil: '',
          email: '',
          nacional: false
        },
        total_neto: 0,
        id_orden_compra: {
          id: 0,
          fecha: '',
          id_cotizacion: null,
          cotizable: false
        },
        incompleta: false
      },
      detailItem: {
        id: 0,
        fecha: '',
        rut_proveedor: {
          rut: '',
          nombre: '',
          representante: '',
          direccion: '',
          id_ciudad: {
            id: 0,
            nombre: '',
            id_region: {
              id: 0,
              nombre: '',
              id_pais: {
                id: 0,
                nombre: ''
              }
            }
          },
          fono: '',
          movil: '',
          email: '',
          nacional: false
        },
        total_neto: 0
      },
      itemsOrden: []
    }),
    computed: {
    },
    watch: {
      dialog (val) {
        val || this.close()
      },
      selectItemPrestamo (val) {
        this.addItem.stock = val.stock
        this.cambioLabel1(val.stock)
      }
    },
    created () {
      this.initialize()
      this.cargarSelectProveedor()
      this.cargarSelectCotizacion()
      this.cargarItems()
    },
    methods: {
      agregarItemModal () {
        this.agregarItem = true
      },
      clickCheckBox (event, nameCheck) {
        for (var i = 0; i < this.checkBoxArray.length; i++) {
          if (this.checkBoxArray[i].name !== nameCheck) {
            this.checkBoxArray[i].checkboxBoolean = false
          }
        }
      },
      agregarOrdenCompra (e) {
        var cotizableCheck = this.checkbox
        axios.post(config.API_LOCATION + '/bodega/orden_compra/', {fecha: '', cotizable: cotizableCheck}) // peticion POST para agregar una nueva orden de Compra
          .then((response) => {
            for (var i = 0; i < this.itemsAOrdenar.length; i++) {
              var idItem = this.itemsAOrdenar[i].item.id
              var idCantidad = this.itemsAOrdenar[i].cantidad
              axios.post(config.API_LOCATION + '/bodega/detalle_adquisicion/', {cantidad: idCantidad, estado: false, id_item: {id: idItem}, id_orden_compra: {id: response.data.id}}) // Petición post para agregar un detalle de adquisición
                .then((response) => {
                  this.initialize()
                  this.dialogAdd = false // cerrar el modal
                  this.snackbar = true // abrir SnackBar
                })
                .catch(e => {
                })
            }
          })
          .catch(e => {
          })
      },
      detalleItem (obj) {
        axios.get(config.API_LOCATION + '/bodega/item/' + obj.item.id + '')
          .then((response) => {
            this.detailItemOrden = response.data
            this.detalleItemModal = true
          })
          .catch(e => {
          })
      },
      editItemModal (item) {
        this.editItemOrden = item
        this.editedIndex = this.itemsAOrdenar.indexOf(item)
        this.editarItemModal = true
      },
      agregarItemAOrdenar () {
        var item = this.selectItem
        var cantidaItem = document.getElementById('cantidad').value
        this.itemsAOrdenar.push({item: item, cantidad: cantidaItem})
        var indexArray = this.itemsSelectOrden.findIndex(x => x.id === item.id)
        this.itemsSelectOrden.splice(indexArray, 1)
        this.agregarItem = false
      },
      asignarOrdenCompra (e) {
        console.log(this.ordenCompraSelected)
        var indexArray = this.checkBoxArray.findIndex(x => x.checkboxBoolean === true)
        var idCotizacion = parseInt(document.getElementsByName(this.checkBoxArray[indexArray].name)[0].getAttribute('cotizacion'))
        axios.put(config.API_LOCATION + '/bodega/orden_compra/' + this.ordenCompraSelected.id, {
          fecha: this.ordenCompraSelected.fecha,
          cotizable: this.ordenCompraSelected.cotizable,
          id_cotizacion: {id: idCotizacion}}) // peticion POST para agregar una nueva orden de Compra
          .then((response) => {
            this.initialize()
            this.dialogAsignar = false
            this.snackbar = true
          })
          .catch(e => {
          })
      },
      itemEliminado (e) {
        var indexArray = this.inputArray.findIndex(x => x.idInput === e)
        this.inputArray.splice(indexArray, 1)
      },
      changeSelectPrestamo (e) {
        this.selectItemPrestamo = e.id
      },
      eliminarItem (item) {
        var indexArray = this.itemsAOrdenar.findIndex(x => x.item === item)
        this.itemsAOrdenar.splice(indexArray, 1)
        this.itemsSelectOrden.push(item.item)
      },
      cargarItems () {
        axios.get(config.API_LOCATION + '/bodega/item/')
          .then((response) => {
            this.itemsSelectOrden = response.data
          })
          .catch(e => {
          })
      },
      editarItemAOrdenar (e) {
        Object.assign(this.itemsAOrdenar[this.editedIndex], this.editItemOrden)
        this.editarItemModal = false
      },
      changeSelectCotizacion (e) {
        var id = e.id
        axios.get(config.API_LOCATION + '/bodega/detalle_adquisicion/' + id + '/cotizacion') // peticion get para llenar el select de cotizaciones
          .then((response) => {
            this.itemsCotizacion = response.data
          })
          .catch(e => {
          })
      },
      onClickChild (value) {
        this.inputArray.push(value)
        console.log(this.inputArray) // someValue
      },
      onChangeSelect (val) {
        this.editedItem.categoria = val.target.value
      },
      cargarSelectProveedor () {
        axios.get(config.API_LOCATION + `/bodega/proveedor/`) // petición GET para llenar el select con los proveedores
          .then((response) => {
            this.proveedores = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectCotizacion () {
        axios.get(config.API_LOCATION + `/bodega/cotizacion/`) // petición GET para cargar el select de cotizaciones
          .then((response) => {
            this.cotizaciones = response.data
          })
          .catch(e => {
          })
      },
      detalleItemCot (cotizacion) {
        axios.get(config.API_LOCATION + '/bodega/detalle_cotizacion/' + cotizacion.id + '/cotizacion') // petición GET para cargar el select de cotizaciones
          .then((response) => {
            this.detailItemsCotizacion = response.data
          })
          .catch(e => {
          })
        this.itemCotizacionDetail = cotizacion
        this.cotizacionDetail = true
        console.log(cotizacion)
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/orden_compra/`) // petición GET para traer todas las ordenes de compra
          .then((response) => {
            console.log(response.data)
            this.items = response.data
          })
          .catch(e => {
          })
      },
      close () {
        this.dialog = false
      },
      clearAddModal () {
        this.$refs.fAgregarSubCat.reset()
        this.dialogAdd = false
      },
      clearEditModal () {
        this.dialogEdit = false
      },
      modalAsignar (item) {
        this.ordenCompraSelected = item
        axios.get(config.API_LOCATION + '/bodega/cotizacion/' + item.id + '/orden_compra') // petición GET para traer todas las cotizaciones que posean la id compra asignada al abrir el modal
          .then((response) => {
            console.log(response.data)
            this.cotizacionesByCompra = response.data
            for (var i = 0; i < response.data.length; i++) {
              if (response.data[i].incompleta !== true) {
                this.checkBoxArray.push({name: 'checkBox' + [i] + '', checkboxBoolean: false})
              }
            }
          })
          .catch(e => {
          })
        this.asignarItem = this.items.indexOf(item) // obtener posición del array
        this.asignarItem = Object.assign({}, item)
        this.dialogAsignar = true
      },
      modalDetalle (item) {
        var id = item.id
        console.log(id)
        axios.get(config.API_LOCATION + '/bodega/detalle_adquisicion/' + id + '/orden') // petición GET para traer todos los detalle adquisicion según la id de orden de compra
          .then((response) => {
            this.itemsOrden = response.data
            this.detailItem = this.items.indexOf(item) // obtener posición del array
            this.detailItem = Object.assign({}, item)
            console.log(this.detailItem)
            this.dialogDetail = true
          })
          .catch(e => {
          })
      },
      cerrarModalDetail () {
        this.dialogDetail = false
      },
      cerrarModalDetailCotizacion () {
        this.cotizacionDetail = false
      }
    }
  }
</script>

<style>
  div .divItemCotizacion{
    border-radius: 5px;
    border-style: solid;
    border-width: 4px;
    border-color: grey;
  }
  .textDivCotizacion{
    text-align: center;
    width:19%;
    margin-top:-4%;
    margin-left:4%;
    background:white;
  }
  .deleteBtn{
    min-width: 0%;
    min-height: 0%;
    margin-top:-7%;
    margin-bottom:-4%;

    margin-left:94%;
    height: 6%;
    text-align: right;
    width:6%;
  }
  .btnOptions{
    min-width: 0%;
    height: 30%;
    text-align: right;
    width:90%;
  }
  .cotizacionIncompleta{
    background-color:red;
    border-radius: 4px;
    margin-bottom: 0%;
  }
  .cotizacionCompleta{
    background-color:rgba(24, 243, 21, 0.70);
    border-radius: 4px;
    margin-bottom: 0%;
  }
</style>