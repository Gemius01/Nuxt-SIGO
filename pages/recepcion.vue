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
  <!-- Dialog  Realizar Recepción -->
    <v-dialog v-model="dialogAdd"  max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Registrar Recepción</v-btn>
       <v-form @submit.prevent="crearRecepcionInicial" lazy-validation>
        <v-card>
          <v-card-title>
            <span class="headline">Registrar Recepción</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs12>
              <v-select label="Proveedor" :items="proveedores" @change="changeProveedor" item-text="nombre" item-value="rut"></v-select>
              </v-flex>
              <v-flex xs12>
              <v-select label="Factura" v-model="selectFactura" :items="compras" @change="changeCompra" item-text="numFactura" item-value="id" ></v-select>
              </v-flex>
              <!-- Contenido Compra -->
             <div v-for="compra in arrayCompra" class="divItemCompra">
              <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs6>
              <v-text-field label="Hora" value="12:00:00" disabled></v-text-field>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Fecha Actual" value="12/07/2018" disabled></v-text-field>
              </v-flex>
                <v-container grid-list-md class="divItemCompra">
                  <v-layout wrap >
                    
                      <v-flex xs4>
                        <v-subheader>Observación</v-subheader>
                      </v-flex>
                      <v-flex xs8>
                        <v-text-field
                          id="observacionText"
                          name="input-7-1"
                          label="Texto"
                          multi-line
                        ></v-text-field>
                      
                    </v-flex>
                  </v-layout>
                </v-container>
              <v-flex xs12>
              <v-text-field label="Fecha Actualización" value="12/07/2018" disabled></v-text-field>
              </v-flex>
              <!-- Lista de Items Compra -->
              <div v-for="(item, key, index) in arrayItemRecepcion" class="divItemCompra">
              <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs4>
              <v-text-field label="Nombre" :value="item.id_item.nombre" disabled></v-text-field>
              </v-flex>
              <v-flex xs4>
              <v-text-field label="Cantidad" type="number" :value="item.cantidad"disabled></v-text-field>
              </v-flex>
              <v-flex xs4 v-if="arrayCheckBox[key].checkboxInicial === false">
              <v-checkbox
              :name="arrayCheckBox[key].nameCheck"
              color="primary"
              label="Recibido"
              v-model="arrayCheckBox[key].checkboxBoolean"
              ></v-checkbox>
              </v-flex>
              <v-flex xs4 v-if="arrayCheckBox[key].checkboxInicial === true">
              <v-checkbox
              :name="arrayCheckBox[key].nameCheck"
              color="primary"
              label="Recibido"
              v-model="arrayCheckBox[key].checkboxBoolean"
              disabled
              ></v-checkbox>
              </v-flex>
              </v-layout>
            </v-container>
            </div>
              </v-layout>
            </v-container>
            </div>
              <!-- Fin Lista de Items Compra -->
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
    <!-- Dialog Realizar Recepción En Un Registro-->
    <v-dialog v-model="dialogRecepcionUnica"  max-width="500px" >
       <v-form @submit.prevent="editarRecepcionCreada" lazy-validation>
        <v-card>
          <v-card-title>
            <span class="headline">Registrar Recepción</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap >
                 <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0"  style="text-align:center;">Fecha : {{recepcionItem.fecha}}</v-card-text>
          </v-card>
        </v-flex>
         <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0"  style="text-align:center;">Fecha Update : {{recepcionItem.fecha_update}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0"  style="text-align:center;">Hora : {{recepcionItem.hora}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs6>
          <v-card dark color="primary">
            <v-card-text class="px-0"  style="text-align:center;">id : {{recepcionItem.id}}</v-card-text>
          </v-card>
        </v-flex>
                <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0"  style="text-align:center;">Motivo : {{recepcionItem.id_compra.id}}</v-card-text>
          </v-card>
        </v-flex>
              <!-- Contenido Compra -->
             <div v-for="compra in compraRecepcion" class="divItemCompra">
              <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs6>
              <v-text-field label="Hora" value="12:00:00" disabled></v-text-field>
              </v-flex>
              <v-flex xs6>
              <v-text-field label="Fecha Actual" value="12/07/2018" disabled></v-text-field>
              </v-flex>
                <v-container grid-list-md class="divItemCompra">
                  <v-layout wrap >
                    
                      <v-flex xs4>
                        <v-subheader>Observación</v-subheader>
                      </v-flex>
                      <v-flex xs8>
                        <v-text-field
                         :value="recepcionItem.observaciones"
                          id="observacionTextUnico"
                          name="input-7-1"
                          label="Texto"
                          multi-line
                        ></v-text-field>
                      
                    </v-flex>
                  </v-layout>
                </v-container>
              <v-flex xs12>
              <v-text-field label="Fecha Actualización" value="12/07/2018" disabled></v-text-field>
              </v-flex>
              <!-- Lista de Items Compra -->
              <div v-for="(item, key, index) in arrayItemRecepcion" class="divItemCompra">
              <v-container grid-list-md>
              <v-layout wrap >
              <v-flex xs4>
              <v-text-field label="Nombre" :value="item.id_item.nombre" disabled></v-text-field>
              </v-flex>
              <v-flex xs4>
              <v-text-field label="Cantidad" type="number" :value="item.cantidad"disabled></v-text-field>
              </v-flex>
              <v-flex xs4 v-if="arrayCheckBox[key].checkboxInicial === false">
              <v-checkbox
              :name="arrayCheckBox[key].nameCheck"
              color="primary"
              label="Recibido"
              v-model="arrayCheckBox[key].checkboxBoolean"
              ></v-checkbox>
              </v-flex>
              <v-flex xs4 v-if="arrayCheckBox[key].checkboxInicial === true">
              <v-checkbox
              :name="arrayCheckBox[key].nameCheck"
              color="primary"
              label="Recibido"
              v-model="arrayCheckBox[key].checkboxBoolean"
              disabled
              ></v-checkbox>
              </v-flex>
              </v-layout>
            </v-container>
            </div>
              </v-layout>
            </v-container>
            </div>
              <!-- Fin Lista de Items Compra -->
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
    <!-- Fin Dialog Realizar Recepción En Un Registro -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Recepciones de Compra</h1>
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
        <td class="text-xs-center">{{ props.item.Hora }}</td>
        <td class="text-xs-center">{{ props.item.id_compra.id }}</td>
        <td class="text-xs-center">{{ props.item.fecha_update }}</td>
        <td class="justify-center layout px-0">
        <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalRecepcion(props.item)" >
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
      textoRules: config.rules,
      active: null,
      checkbox: false,
      arrayCompra: [],
      arrayCheckBox: [],
      arrayItemRecepcion: [],
      activeForm: 1,
      errorMessages: [],
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Orden de Compra
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Orden de Compra
      dialogAsignar: false, // prop para abrir y cerrar modal de Asignar Cotización
      dialogRecepcionUnica: false, // prop para abrir y cerrar modal detalle cotizacion
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      devolverIndex: -1,
      labelCantidad: 'Disponible : ' + 0 + '',
      value: '',
      selectItem: 0,
      search: '',
      checkBoxArray: [], // Array boolean que estarán dentor de la iteraciones de cotización en el modal asignar cotizacion
      inputArray: [],
      // Props Snack Bar
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      // Fin Props Snack Bar
      headers: [ // Encabezados de  la tabla
        { text: 'Fecha', value: 'fecha', width: '20%', align: 'center' },
        { text: 'Hora', value: 'hora', width: '20%', align: 'center' },
        { text: 'Compra', value: 'compra', width: '20%', align: 'center' },
        { text: 'Fecha Actualización', value: 'fecha_update', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      items: [],
      proveedores: [],
      compras: [],
      selectFactura: [],
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
      recepcionItem: {id: 0, id_compra: {id: 0}, fecha: '', fecha_update: ''},
      recepcionUnica: [],
      compraRecepcion: [],
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
      this.cargarSelectCompras()
    },
    methods: {
      clickCheckBox (event, nameCheck) {
        console.log(this.arrayCheckBox)
      },
      changeProveedor (e) {
        axios.get(config.API_LOCATION + '/bodega/compra/' + e + '/proveedor') // Petición post para agregar un detalle de adquisición
          .then((response) => {
            this.compras = response.data
          })
          .catch(e => {
          })
      },
      changeCompra (e) {
        axios.get(config.API_LOCATION + '/bodega/compra/' + e + '') // Petición post para agregar un detalle de adquisición
          .then((response) => {
            this.arrayCompra = [response.data]
            console.log(this.arrayCompra)
            axios.get(config.API_LOCATION + '/bodega/detalle_adquisicion/' + response.data.id_orden_compra.id + '/orden') // Petición post para agregar un detalle de adquisición
              .then((response) => {
                this.arrayItemRecepcion = response.data
                this.arrayCheckBox = []
                for (var i = 0; i < response.data.length; i++) {
                  console.log(response.data[i].estado)
                  this.arrayCheckBox.push({nameCheck: 'checkBox' + [i] + '', checkboxBoolean: response.data[i].estado, checkboxInicial: response.data[i].estado, detalle_adquisicion: response.data[i]})
                }
                console.log(this.arrayCheckBox)
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
      },
      crearRecepcionInicial (e) {
        var observacion = document.getElementById('observacionText').value
        var compra = this.selectFactura
        axios.post(config.API_LOCATION + '/bodega/recepcion_compra/', {
          fecha: '',
          hora: null,
          observaciones: '' + observacion + '',
          fecha_update: '',
          id_compra: {id: compra}
        })
          .then((response) => {
            for (var i = 0; i < this.arrayCheckBox.length; i++) {
              var cantidadComprada = parseInt(this.arrayCheckBox[i].detalle_adquisicion.cantidad)
              console.log('cantidadComprada ' + cantidadComprada)
              console.log(this.arrayCheckBox[i].checkboxBoolean + ' === ' + ' true')
              if (this.arrayCheckBox[i].checkboxBoolean !== this.arrayCheckBox[i].checkboxInicial) {
                axios.get(config.API_LOCATION + '/bodega/item/' + this.arrayCheckBox[i].detalle_adquisicion.id_item.id + '')
                  .then((responseItem) => {
                    var stockFinal = cantidadComprada + responseItem.data.stock
                    axios.put(config.API_LOCATION + '/bodega/item/' + responseItem.data.id + '', {
                      nombre: responseItem.data.nombre,
                      modelo: responseItem.data.modelo,
                      stock: stockFinal,
                      stockCritico: responseItem.data.stockCritico,
                      id_marca: {id: responseItem.data.id_marca.id},
                      id_unidad_medida: {id: responseItem.data.id_unidad_medida.id},
                      id_subcategoria: {id: responseItem.data.id_subcategoria.id}
                    })
                      .then((response) => {
                        for (var val = 0; val < this.arrayCheckBox.length; val++) {
                          if (this.arrayCheckBox[val].checkboxBoolean !== this.arrayCheckBox[val].checkboxInicial) {
                            var detailAdquisicion = this.arrayCheckBox[val].detalle_adquisicion
                            axios.put(config.API_LOCATION + '/bodega/detalle_adquisicion/' + detailAdquisicion.id + '', {
                              cantidad: detailAdquisicion.cantidad,
                              id_item: {id: detailAdquisicion.id_item.id},
                              estado: true,
                              id_orden_compra: {id: detailAdquisicion.id_orden_compra.id}
                            })
                              .then((response) => {
                                console.log('Sali de DETALLEEEE')
                              })
                              .catch(e => {
                                console.log(e)
                              })
                          }
                        }
                      })

                      .catch(e => {
                        console.log(e)
                      })
                  })
                  .catch(e => {
                  })
              }
            }
            this.initialize()
            this.dialogAdd = false
          })
          .catch(e => {
          })
      },
      editarRecepcionCreada (e) {
        var observacion = document.getElementById('observacionTextUnico').value
        axios.put(config.API_LOCATION + `/bodega/recepcion_compra/` + this.recepcionItem.id + '', {
          fecha: this.recepcionItem.fecha,
          hora: this.recepcionItem.hora,
          observaciones: '' + observacion + '',
          fecha_update: this.recepcionItem.fecha_update,
          id_compra: {id: this.recepcionItem.id_compra.id}
        })
          .then((response) => {
            for (var i = 0; i < this.arrayCheckBox.length; i++) {
              var cantidadComprada = parseInt(this.arrayCheckBox[i].detalle_adquisicion.cantidad)
              console.log('cantidadComprada ' + cantidadComprada)
              console.log(this.arrayCheckBox[i].checkboxBoolean + ' === ' + ' true')
              if (this.arrayCheckBox[i].checkboxBoolean !== this.arrayCheckBox[i].checkboxInicial) {
                axios.get(config.API_LOCATION + '/bodega/item/' + this.arrayCheckBox[i].detalle_adquisicion.id_item.id + '')
                  .then((responseItem) => {
                    var stockFinal = cantidadComprada + responseItem.data.stock
                    axios.put(config.API_LOCATION + '/bodega/item/' + responseItem.data.id + '', {
                      nombre: responseItem.data.nombre,
                      modelo: responseItem.data.modelo,
                      stock: stockFinal,
                      stockCritico: responseItem.data.stockCritico,
                      id_marca: {id: responseItem.data.id_marca.id},
                      id_unidad_medida: {id: responseItem.data.id_unidad_medida.id},
                      id_subcategoria: {id: responseItem.data.id_subcategoria.id}
                    })
                      .then((response) => {
                        for (var val = 0; val < this.arrayCheckBox.length; val++) {
                          if (this.arrayCheckBox[val].checkboxBoolean !== this.arrayCheckBox[val].checkboxInicial) {
                            var detailAdquisicion = this.arrayCheckBox[val].detalle_adquisicion
                            axios.put(config.API_LOCATION + '/bodega/detalle_adquisicion/' + detailAdquisicion.id + '', {
                              cantidad: detailAdquisicion.cantidad,
                              id_item: {id: detailAdquisicion.id_item.id},
                              estado: true,
                              id_orden_compra: {id: detailAdquisicion.id_orden_compra.id}
                            })
                              .then((response) => {
                                console.log('Sali de DETALLEEEE')
                              })
                              .catch(e => {
                                console.log(e)
                              })
                          }
                        }
                      })

                      .catch(e => {
                        console.log(e)
                      })
                  })
                  .catch(e => {
                  })
              }
            }
            this.initialize()
            this.dialogRecepcionUnica = false
          })
          .catch(e => {
          })
      },
      cargarSelectProveedor () {
        axios.get(config.API_LOCATION + `/bodega/proveedor/`) // petición GET para llenar el select con los proveedores
          .then((response) => {
            this.proveedores = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectCompras () {
        axios.get(config.API_LOCATION + `/bodega/compra/`) // petición GET para cargar el select de cotizaciones
          .then((response) => {
            this.compras = response.data
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
        axios.get(config.API_LOCATION + `/bodega/recepcion_compra/`) // petición GET para traer todas las ordenes de compra
          .then((response) => {
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
      modalRecepcion (recepcion) {
        console.log(recepcion)
        this.recepcionItem = recepcion
        axios.get(config.API_LOCATION + '/bodega/compra/' + recepcion.id_compra.id + '') // Petición post para agregar un detalle de adquisición
          .then((response) => {
            this.compraRecepcion = [response.data]
            console.log(this.compraRecepcion)
            axios.get(config.API_LOCATION + '/bodega/detalle_adquisicion/' + response.data.id_orden_compra.id + '/orden') // Petición post para agregar un detalle de adquisición
              .then((response) => {
                this.arrayItemRecepcion = response.data
                this.arrayCheckBox = []
                for (var i = 0; i < response.data.length; i++) {
                  console.log(response.data[i].estado)
                  this.arrayCheckBox.push({nameCheck: 'checkBox' + [i] + '', checkboxBoolean: response.data[i].estado, checkboxInicial: response.data[i].estado, detalle_adquisicion: response.data[i]})
                }
                console.log(this.arrayCheckBox)
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
        this.dialogRecepcionUnica = true
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
  div .divItemCompra{
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