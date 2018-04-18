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
  <!-- Dialog Agregar Tipo -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Registrar Compra</v-btn>
            <v-form @submit.prevent="checkForm" v-model="valid" ref="fAgregarCompra" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Registrar Compra</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
                <v-flex xs12>
                <v-select item-value="id" item-text="id" @change="onChangeSelectOrden" :items="ordenCompraItem" v-model="addItem.ordenCompra" label="Orden de Compra" single-line
                ></v-select>
              </v-flex>
              <v-flex xs12 class="ordenDesc" v-if="ordenCompraSelected.cotizable === true" >
                <h4>Haz seleccionado una orden de compra con cotización</h4>
              </v-flex>
              <v-flex xs12 class="ordenDesc"  v-if="ordenCompraSelected.cotizable === false">
               <h4>Haz seleccionado una orden de compra sin cotización</h4>
              </v-flex>
                

                <v-flex class="divItemCotizacion"  xs12 v-if="ordenCompraSelected.cotizable != null">
                  <!-- Cotizada -->
                  <v-container grid-list-md v-if="ordenCompraSelected.cotizable === true" >
                    <v-layout wrap>
                    <v-flex xs12>
                      <v-text-field label="Fecha" v-model="fechaBack.fecha" disabled required> 
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12>
                      <v-text-field label="N° Factura" id="nFacturaCotizable"  required> 
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12>
                      <v-text-field label="Total Neto"  id="nTotalNetoCotizable" required> 
                      </v-text-field>
                    </v-flex>
                    </v-layout>
                  </v-container>
                  <!-- Fin Cotizada -->
                  <!-- Sin Cotización -->
                  <v-container grid-list-md v-if="ordenCompraSelected.cotizable === false" >
                    <v-layout wrap>
                    <v-flex xs12>
                      <v-text-field label="Fecha"  v-model="fechaBack.fecha" disabled required> 
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12>
                      <v-text-field label="N° Factura"   id="nFacturaNoCotizable" required> 
                      </v-text-field>
                    </v-flex>
                    <v-flex xs12>
                      <v-text-field label="Total Neto"  id="nTotalNetoNoCotizable" required> 
                      </v-text-field>
                    </v-flex>
                    <v-select :items="proveedores" item-text="rut" v-model="proveedorSelected" item-value="rut" label="Proveedor" single-line>
                    </v-select>
                    <!-- ITEMS -->
                    <div v-for="(item, key, index) in this.prueba"  class="divItem">
                      <h4 class="textDiv">Item</h4>
                      <v-container grid-list-md>
                                <v-layout wrap>
                                  <v-flex xs4 style="display:none;">
                        <v-text-field   label="Nombre" :id="inputItems[key].idItem" :value="item.id_item.id" disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4>
                        <v-text-field  label="Nombre" :value="item.id_item.nombre"  disabled></v-text-field>
                      </v-flex>
                      <v-flex xs4>
                        <v-text-field   label="Cantidad" :value="item.cantidad" :id="inputItems[key].idCantidad" disabled></v-text-field>
                      </v-flex>
                       <v-flex xs4>
                        <v-text-field  label="Valor Unit" type="number" :id="inputItems[key].idValor" required></v-text-field>
                        </v-flex>
                        </v-layout>
                        </v-container> 
                        </div>
                    <!-- Fin Items -->
                    </v-layout>
                  </v-container>
                  <!-- Fin Sin Cotización -->
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
<v-dialog v-model="dialog3" max-width="500px">
            <v-form @submit.prevent="agregarMarca" v-model="valid" ref="fagregarMarca" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Marca</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Nombre"  :rules="textoRules" :counter="20" name="nombre" v-model="addItemMarca.nombre" required></v-text-field>
                </v-flex>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="dialog3=false">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Dialog Editar Tipo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <form @submit.prevent="editInsumo">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Item</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 style="display:none;">
                <v-text-field label="id" v-model="editedItem.id" name="idEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 style="display:none;">
                <v-text-field label="Stock" v-model="editedItem.stock" name="stockEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Nombre" v-model="editedItem.nombre" name="nombreEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Modelo" :counter="20" :rules="textoRules" v-model="editedItem.modelo" name="modeloEdit"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field label="Stock Critico" type="number" :counter="20" :rules="textoRules" v-model="editedItem.stockCritico" name="stockCriticoEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md11>
                <v-select 
                id="selectMarcaEdit" 
                :items="marcaItem"
                item-text="nombre"
                item-value="id"
                @select='onChangeSelect0'
                v-model="editedItem.id_marca.id"
                :rules="[() => !!this.editedItem.id || 'This field is required']"
                search-input autocomplete label="Marca" 
                single-line>
                </v-select>
        </v-flex>
              <v-flex xs12 sm6 md1>
                <v-btn  @click.stop="dialog3 = !dialog3" icon small slot="activator" class="mx-0" fab dark color="indigo"><v-icon dark>add</v-icon></v-btn></v-flex>

              <v-flex xs12>
                <v-select 
                id="selectUnidadEdit" 
                :items="unidadMedidaItem"
                item-text="medida"
                item-value="id"
                @select='onChangeSelect1'
                v-model="editedItem.id_unidad_medida.id"
                :rules="[() => !!this.editedItem.id || 'This field is required']"
                search-input autocomplete label="Unidad de medida" 
                single-line>
                </v-select>
        </v-flex>
        <v-flex xs12>
                <v-select 
                id="selectSubEdit" 
                :items="subcategoriasItem" 
                item-text="nombre" 
                item-value="id" 
                v-model="editedItem.id_subcategoria.id" 
                @select='onChangeSelect' 
                search-input 
                autocomplete 
                label="Subcategoria">
                </v-select>
        </v-flex>
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
    <!-- Fin Dialog Editar Tipo -->
    <!-- Dialog Detalle Tipo -->
        <v-dialog v-model="dialogDetail" max-width="550px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Compra </h1></v-card-title>
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
              <v-list-tile-title>N° Factura</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.numFactura }}</v-list-tile-title>
            </v-list-tile>
             <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content  v-for="(Item,i) in 1" :key="i">
        <div slot="header"><h4>Información de la Orden de Compra</h4></div>
        <v-list dense>

            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Id Orden</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Id Cotización</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Cotizable</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.cotizable }}</v-list-tile-title>
            </v-list-tile>
</v-list>
      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
         <v-flex xs12>
        <v-expansion-panel focusable>
      <v-expansion-panel-content  v-for="(Item,i) in 1" :key="i">
        <div slot="header"><h4>Información de la Cotizacion </h4></div>
 <v-list dense>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Rut Proveedor</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.rut }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Orden de compra</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.orden_compra }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Incompleta</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.incompleta }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title></v-list-tile-title>
              <v-list-tile-title class="text-lg-center"><h4>Total Neto:</h4></v-list-tile-title>
              <v-list-tile-title><h4>{{ detailItem.id_orden_compra.id_cotizacion.total_neto }}</h4></v-list-tile-title>
            </v-list-tile>
            
</v-list>

      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
         <v-flex xs12 class="hoverMouse">
        <v-expansion-panel focusable>
      <v-expansion-panel-content  v-for="(Item,i) in 1" :key="i">
        <div slot="header"><h4>Información del Proveedor</h4></div>
  <v-list dense>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Rut</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.rut }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Representante</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.representante }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Dirección</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.direccion }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Ciudad</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.id_ciudad.nombre }}, {{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.id_ciudad.id_region.nombre }}, {{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.id_ciudad.id_region.id_pais.nombre }} </v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Teléfono</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.fono }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Movil</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.movil }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Email</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_orden_compra.id_cotizacion.rut_proveedor.email }}</v-list-tile-title>
            </v-list-tile>
</v-list>

      </v-expansion-panel-content>
    </v-expansion-panel>
        </v-flex>
        <v-list-tile class="hoverMouse">
              <v-list-tile-title><h4>TOTAL NETO</h4></v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.total_neto}}</v-list-tile-title>
            </v-list-tile>
        
           
          </v-list>
        
         
        
      
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
        <h1>Compras</h1>
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
        <td class="text-xs-center">{{ props.item.fecha }}</td>
        <td class="text-xs-center">{{ props.item.numFactura }}</td>
        <td class="text-xs-center">{{ props.item.total_neto }}</td>
        <td class="text-xs-center">{{ props.item.id_orden_compra.id }}</td>
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
  import item from '../components/compra/item.vue'
  export default {
    components: { config, item },
    data: () => ({
      prueba: [],
      subcatego: 0,
      subcategoriasItem: [],
      marca: 0,
      selectSubEdit: 0,
      marcaItem: [],
      unidaddemedida: 0,
      unidadMedidaItem: [],
      textoRules: [(v) => !!v || 'Campo vacío', v => (v && v.length <= 20) || 'Máximo de caracteres'],
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Tipo
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Tipo
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Tipo
      dialogDelete: false,
      dialog3: false, // prop para abrir y cerrar modal de Delete Tipo
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      proveedorSelected: '',
      cotizacionByCompra: [],
      ordenCompraSelected: [],
      inputValores: [],
      proveedores: [],
      inputItems: [{idValor: 'asd'}],
      search: '',
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      fechaBack: '',
      text: 'Se ha agregado con exito',
      headers: [ // Encabezados de  la tabla
        {
          text: 'ID',
          value: 'id',
          sortable: true,
          width: '15%',
          align: 'center'
        },
        { text: 'Fecha', value: 'fecha', width: '15%', align: 'center' },
        { text: 'Nº Factura', value: 'numFactura', width: '15%', align: 'center' },
        { text: 'Total  Neto', value: 'totalneto', width: '15%', align: 'center' },
        { text: 'Orden de compra', value: 'id_orden_compra', width: '15%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '15%', align: 'center' }
      ],
      items: [],
      ordenCompraItem: [],
      valid: true,
      addItem: {
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stock_critico: ''
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        nombre: '',
        modelo: '',
        id_marca: '',
        id_subcategoria: '',
        id_unidad_medida: '',
        stockCritico: '',
        stock: ''
      },
     detailItem: {
        id: 0,
        fecha: '',
        numFactura: 0,
        id_orden_compra: {
          id: 0,
          fecha: '',
          id_cotizacion: {
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
              nacional: ''
            },
            total_neto: 0,
            orden_compra: 0,
            incompleta: ''
          },
          cotizable: ''
        },
        total_neto: 0
      },
      addItemMarca: {
        id: 0,
        nombre: ''
      },
      deleteItem: {
        id: 0,
        nombre: ''
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
      this.cargarSelectCat()
      this.cargarSelectUnidad()
      this.cargarSelectOrden()
      this.cargarSelectProveedor()
      this.cargarFechaBack()
    },
    methods: {
      pushInputValores (e) {
        this.inputValores.push(e)
      },
      checkForm (e) {
        console.log(e)
        if (this.ordenCompraSelected.cotizable === null) {
        } else if (this.ordenCompraSelected.cotizable === true) {
          this.registrarCompraCotizable()
        } else if (this.ordenCompraSelected.cotizable === false) {
          this.registrarCompraNoCotizable()
        }
      },
      registrarCompraCotizable () {
        console.log('registrarCotizable')
        var nFactura = document.getElementById('nFacturaCotizable').value
        var nTotalNeto = document.getElementById('nTotalNetoCotizable').value
        console.log(nFactura + ' ' + nTotalNeto)
        axios.post(config.API_LOCATION + '/bodega/compra/', {fecha: '', numFactura: nFactura, total_neto: nTotalNeto, id_orden_compra: {id: this.ordenCompraSelected.id}}) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.initialize()
            this.dialogAdd = false
          })
          .catch(e => {
          })
      },
      registrarCompraNoCotizable () {
        console.log('registrarNOCotizable')
        var nFactura = document.getElementById('nFacturaNoCotizable').value
        var nTotalNeto = document.getElementById('nTotalNetoNoCotizable').value
        var proveedor = this.proveedorSelected
        axios.post(config.API_LOCATION + '/bodega/compra/', {fecha: '', numFactura: nFactura, total_neto: nTotalNeto, id_orden_compra: {id: this.ordenCompraSelected.id}}) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            axios.post(config.API_LOCATION + '/bodega/cotizacion/', {fecha: '', rut_proveedor: {rut: proveedor}, total_neto: nTotalNeto, id_orden_compra_selec: this.ordenCompraSelected.id, incompleta: false}) // petición GET a Tipo para traer a todos los objetos "tipo"
              .then((responseCotizacion) => {
                for (var i = 0; i < this.inputItems.length; i++) {
                  var valorUnit = document.getElementById(this.inputItems[i].idValor).value
                  var numCantidad = document.getElementById(this.inputItems[i].idCantidad).value
                  var idItem = document.getElementById(this.inputItems[i].idItem).value
                  console.log(valorUnit + ' ' + numCantidad + ' ' + idItem)
                  axios.post(config.API_LOCATION + '/bodega/detalle_cotizacion/', {id_item: {id: idItem}, valor_unitario: valorUnit, cantidad: numCantidad, id_cotizacion: {id: responseCotizacion.data.id}}) // petición GET a Tipo para traer a todos los objetos "tipo"
                    .then((response) => {
                      axios.get(config.API_LOCATION + '/bodega/orden_compra/' + this.ordenCompraSelected.id + '') // petición GET a Tipo para traer a todos los objetos "tipo"
                        .then((responseOrdenEdit) => {
                          axios.put(config.API_LOCATION + '/bodega/orden_compra/' + this.ordenCompraSelected.id + '', {
                            fecha: responseOrdenEdit.data.fecha,
                            id_cotizacion_selec: {id: responseCotizacion.data.id},
                            cotizable: responseOrdenEdit.data.cotizable
                          }) // petición GET a Tipo para traer a todos los objetos "tipo"
                            .then((response) => {
                              this.initialize()
                              this.dialogAdd = false
                            })
                            .catch(e => {
                            })
                        })
                        .catch(e => {
                        })
                    })
                    .catch(e => {
                    })
                }
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
        console.log(nFactura + ' ' + nTotalNeto + ' ' + proveedor)
      },
      onChangeSelectOrden (e) {
        if (!this.ordenCompraSelected.cotizable) {
          axios.get(config.API_LOCATION + '/bodega/detalle_adquisicion/' + e + '/orden') // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
            .then((response) => {
              this.inputItems = []
              this.prueba = response.data
              for (var i = 0; i < response.data.length; i++) {
                this.inputItems.push({idValor: 'idValor' + [i] + '', idCantidad: 'idCantidad' + [i] + '', idItem: 'idItem' + [i] + ''})
              }
            })
            .catch(e => {
            })
        }
        this.ordenCompraSelected = []
        this.cotizacionByCompra = []
        axios.get(config.API_LOCATION + `/bodega/orden_compra/` + e) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.ordenCompraSelected = response.data
            console.log(this.ordenCompraSelected)
            axios.get(config.API_LOCATION + `/bodega/cotizacion/` + response.data.id_cotizacion_selec.id) // petición GET a Tipo para traer a todos los objetos "tipo"
              .then((response) => {
                console.log(response.data)
                this.cotizacionByCompra = response.data
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
      },
      onChangeSelect (val) {
        console.log(val)
        this.editedItem.id_subcategoria.nombre = val.target.value
      },
      onChangeSelect0 (val) {
        console.log(val)
        this.editedItem.id_marca.nombre = val.target.value
      },
      onChangeSelect1 (val) {
        this.editedItem.id_unidad_medida.nombre = val.target.value
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/compra/`) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.items = response.data// LLenado del array "items"
          })
          .catch(e => {
          })
      },
      cargarFechaBack () {
        axios.get(config.API_LOCATION + `/fecha/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.fechaBack = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectOrden () {
        axios.get(config.API_LOCATION + `/bodega/orden_compra/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.ordenCompraItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectCat () {
        axios.get(config.API_LOCATION + `/bodega/subcategoria/tipo/insumo/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.subcategoriasItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectUnidad () {
        axios.get(config.API_LOCATION + `/bodega/unidad_medida/`)// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.unidadMedidaItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectProveedor () {
        axios.get(config.API_LOCATION + `/bodega/proveedor/`)// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.proveedores = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectCatt () {
        axios.get(config.API_LOCATION + `/bodega/subcategoria/tipo/insumo/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            for (var i = response.data.length; i >= 0; i--) {
              this.subcategoriasItem.push({header: '' + response.data[i - 1].id_categoria.nombre + ''})
              for (var e = response.data.length; e >= 0; e--) {
                if (response.data[i - 1].id_categoria.nombre === response.data[e - 1].id_categoria.nombre) {
                  this.subcategoriasItem.push({name: '' + response.data[i - 1].nombre + ''})// LLenado del array"items"
                }
              }
            }
          })
          .catch(e => {
          })
      },
      agregarInsumos (e) { // función para agregar un nuevo Tipo
        var nombre = this.addItem.nombre
        var modelo = this.addItem.modelo
        var stockCri = this.addItem.stockCritico
        var stock = 0
        var marca = this.addItem.marca
        var unidadmedida = this.addItem.unidaddemedida
        var subCategoria = this.addItem.subcatego
        console.log(marca)
        console.log(unidadmedida)
        console.log(subCategoria)
        axios.post(config.API_LOCATION + '/bodega/item/', { // petición POST a Tipo para agregar
          nombre: '' + nombre + '',
          id_marca: { id: marca },
          modelo: '' + modelo + '',
          stock: '' + stock + '',
          stockCritico: '' + stockCri + '',
          id_subcategoria: { id: subCategoria },
          id_unidad_medida: { id: unidadmedida }
        }
        )
          .then((response) => {
            this.initialize() // push al array de items
            this.dialogAdd = false // cerrar el modal
            this.snackbar = true
          })
      },
      editInsumo (e) { // función para editar la Subcategoría
        var id = this.editedItem.id// obtener id del objeto que se desea editar formulario
        var nombre = this.editedItem.nombre// obtener nombre del objeto que se desea editar
        var modelo = this.editedItem.modelo
        var stockCritico = this.editedItem.stockCritico
        var marca = this.editedItem.id_marca.id
        var stock = this.editedItem.stock
        var unidadmedida = this.editedItem.id_unidad_medida.id
        console.log(marca)
        var subCategoria = this.editedItem.id_subcategoria.id
        console.log(subCategoria)
        axios.put(config.API_LOCATION + '/bodega/item/' + id + '', {// petición put para editar el tipo
          nombre: '' + nombre + '',
          id_marca: { id: marca },
          modelo: '' + modelo + '',
          stock: '' + stock + '',
          stockCritico: '' + stockCritico + '',
          id_subcategoria: { id: '' + subCategoria + '' },
          id_unidad_medida: { id: '' + unidadmedida + '' }}
        )
          .then(response => {
            Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
            this.dialogEdit = false // cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      agregarMarca (e) { // función para agregar un nuevo Marca
        var Marca = this.addItemMarca.nombre
        axios.post(config.API_LOCATION + '/bodega/marca/', { // petición POST a Marca para agregar
          nombre: '' + Marca + ''
        })
          .then((response) => {
            this.dialog3 = false
            this.cargarSelectMarca()
            this.snackbar = true
          })
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      modalDelete (item) { // función para editar el tipo
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteInsumo () { // función para eliminar un tipo
        var id = this.deleteItem.id // id del objeto a eliminar
        console.log(this.items.length)
        axios.delete(config.API_LOCATION + '/bodega/item/' + id + '') // petición DELETE a tipo para poder eliminar un tipo
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
      marcaClose () {
        this.dialog3 = true
        this.dialogAdd = false
      },
      clearAddModal () {
        // this.$refs.fAgregarCategoriaH.reset()
        this.dialogAdd = false
        this.dialog3 = false
      },
      clear () {
        // this.$refs.fAgregarCategoriaH.reset()
        this.dialogAdd = true
        this.dialog3 = false
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
  .ordenDesc{
    background-color: orange;
    border-radius: 3px;
    text-align: center;
  }
</style>