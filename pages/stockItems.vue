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
    <!-- Dialog Eliminar Merma -->
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
            <v-card-text class="px-0" v-model="deleteItem.fecha" style="text-align:center;">Fecha : {{deleteItem.fecha}}</v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.motivo" style="text-align:center;">Motivo : {{deleteItem.motivo}}</v-card-text>
          </v-card>
          </v-flex>
          <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.responsable" style="text-align:center;">Responsable : {{deleteItem.responsable}}</v-card-text>
          </v-card>
          </v-flex>
          <v-flex xs12>
          <v-card dark color="primary">
            <v-card-text class="px-0" v-model="deleteItem.id_item" style="text-align:center;">Item : {{deleteItem.id_item.nombre}}</v-card-text>
          </v-card>
          </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialogDelete = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="deleteMerma">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Merma-->
  <!-- Dialog Agregar Merma -->
    <v-dialog v-model="dialogAdd" max-width="500px">
      <v-btn dark color="primary"  slot="activator" >Agregar Merma</v-btn>
            <v-form @submit.prevent="agregarMerma" v-model="valid" ref="fAgregarMerma" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Agregar Merma</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Motivo"  :rules="parrafoRule" :counter="100" name="motivoMerma" v-model="addItem.motivo" required></v-text-field>
                <v-text-field label="Responsable" :rules="[v => this.rutValidado || 'Rut Invalido']" v-on:keyup="checkRut" :counter="20" name="responsableMerma" v-model="addItem.responsable" required></v-text-field>
                <v-flex xs9>
                <v-select
                  id="selectItem"
                  :items="itemMerma"
                  item-text="nombre"
                  v-model="itemMermaSelectID"
                  search-input
                  value= 'id'
                  v-on:change='onChangeSelectAgregar'
                  :rules="[v => this.selectValidado || 'Campo Vacío']"
                  required
                  autocomplete
                  label="Nombre Item"
                  single-line
                  ></v-select>
                </v-flex>
                <v-text-field type="number" label="Cantidad"  :rules="numberRule" :counter="20" name="cantidadMerma" v-model="addItem.cantidad" required></v-text-field>
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
    <!-- Fin Dialog Agregar Merma -->
    <!-- Dialog Editar Merma -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editMerma" ref="fEditarMerma">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Merma</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Id" v-model="editedItem.id" name="idEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Fecha" v-model="editedItem.fecha" name=""></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Motivo" v-model="editedItem.motivo" name=""></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Responsable" v-model="editedItem.responsable" name=""></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Nombre Item" v-model="editedItem.id_item" name=""></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4 style="display:none;">
                <v-text-field label="Cantidad" v-model="editedItem.cantidad" name=""></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Fecha" v-model="editedItem.fecha" name="fechaEdit" disabled></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Motivo" :counter="100" :rules="parrafoRule" v-model="editedItem.motivo" name="motivoEdit"></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field label="Responsable" :counter="20" :rules="textRule" v-model="editedItem.responsable" name="responsableEdit" disabled></v-text-field>
              </v-flex>
              <v-flex xs9>
               <v-select
            id="selectCatEdit"
            :items="itemMerma"
            item-text="nombre"
            item-value="id"
            @select='onChangeSelect'
            v-model="editedItem.id_item.id"
            :rules="[() => !!this.editedItem.id || 'This field is required']"
            search-input
            autocomplete
            label="Categoría"
            single-line
            disabled
          ></v-select>

        </v-flex>

              <v-flex xs12 sm12 md12>
                <v-text-field label="Cantidad" type="number" :counter="20" :rules="numberRule" v-model="editedItem.cantidad" name="cantidadEdit"></v-text-field>
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
    <!-- Fin Dialog Editar Merma -->
    <!-- Dialog Detalle Merma -->
      <v-dialog v-model="dialogDetail" max-width="500px">
      <v-card>
         <v-card-title><h1> Detalle de Merma </h1></v-card-title>
          <v-divider></v-divider>

        <v-list dense >
        <v-list-tile class="hoverMouse">
              <v-list-tile-title>ID</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fecha }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fecha</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.motivo }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>N° Factura</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.responsable }}</v-list-tile-title>
            </v-list-tile>
          </v-list>
  
      <v-expansion-panel>
      <v-expansion-panel-content v-for="(Item,i) in 1" :key="i">
        <div slot="header">Item</div>
        <v-list dense>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_item.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Marca</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_item.id_marca.nombre }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title>Modelo</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_item.modelo }}</v-list-tile-title>
            </v-list-tile>
             <v-list-tile class="hoverMouse">
              <v-list-tile-title>Stock</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_item.stock }}</v-list-tile-title>
            </v-list-tile>

            <v-list-tile class="hoverMouse">
                <v-list-tile-title>Medida</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.id_item.id_unidad_medida.medida }}</v-list-tile-title>
              </v-list-tile>

            <v-list-tile class="hoverMouse">
                <v-list-tile-title>Subcategoria</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailItem.id_item.id_subcategoria.nombre }}</v-list-tile-title>
              </v-list-tile>

            </v-list>
    </v-expansion-panel-content>
    </v-expansion-panel>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>

    </v-dialog>
    <!-- Fin Dialog Editar Merma -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>LISTA ACTIVOS</h1>
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
        <td class="text-xs-center" style="display:none;">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.fecha }}</td>
        <td class="text-xs-center" style ="display:none;" >{{ props.item.motivo }}</td>
        <td class="text-xs-center">{{ props.item.responsable }}</td>
        <td class="text-xs-center">{{ props.item.id_item.nombre }}</td>
        <td class="text-xs-center">{{ props.item.cantidad }}</td>
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
  import validaciones from '../validaciones.vue'
  export default {
    components: { config },
    data: () => ({
      textRule: validaciones.textoRules,
      numberRule: validaciones.numberRules,
      parrafoRule: validaciones.parrafoRules,
      emailRule: validaciones.emailRules,
      rutValidado: false,
      selectValidado: true,
      selectJustificacion: false,
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Merma
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Merma
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Merma
      dialogDelete: false, // prop para abrir y cerrar modal de Delete Merma
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      itemMermaSelectID: {},
      itemMermaSelectIDEdit: {},
      search: '',
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      itemMerma: [],
      cantidadInicialEdit: 0,
      headers: [ // Encabezados de  la tabla
        { text: 'Fecha', value: 'fecha', width: '20%', align: 'center' },
        { text: 'Responsable', value: 'responsable', width: '20%', align: 'center' },
        { text: 'Nombre Item', value: 'id_item', width: '20%', align: 'center' },
        { text: 'Cantidad', value: 'cantidad', width: '20%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
      ],
      items: [],
      valid: true,
      addItem: {
        id: 0,
        fecha: '',
        motivo: '',
        responsable: '',
        id_item: '',
        cantidad: 0
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        id: 0,
        fecha: '',
        motivo: '',
        responsable: '',
        id_item: '',
        cantidad: 0
      },
      detailItem: {
        id: 0,
        fecha: '',
        motivo: '',
        responsable: '',
        id_item: { id_unidad_medida: {medida: ''},
          id_marca: {nombre: ''},
          id_subcategoria: {nombre: ''}},
        cantidad: 0,
        medida: '',
        subcategoria: ''
      },
      deleteItem: {
        id: 0,
        fecha: '',
        motivo: '',
        responsable: '',
        id_item: '',
        cantidad: 0
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
      this.cargarSelectItem()
    },
    methods: {
      checkRut: function (rut) {
        var valorOriginal = rut.target.value.replace('.', '')
        var valor = rut.target.value.replace('.', '')
        var cuerpo = null
        var dv = null
        for (var j = 1; j <= valorOriginal.length; j++) {
          valor = valor.replace('.', '')
          valor = valor.replace('-', '')
        }
        // Despejar Guión
        valor = valor.replace('-', '')
        // Aislar Cuerpo y Dígito Verificador
        cuerpo = valor.slice(0, -1)
        dv = valor.slice(-1).toUpperCase()
        // Formatear RUN
        rut.value = cuerpo + '-' + dv
        // Si no cumple con el mínimo ej. (n.nnn.nnn)
        if (cuerpo.length < 7) { this.rutValidado = false } else {
          var suma = 0
          var multiplo = 2

          // Para cada dígito del Cuerpo
          for (var i = 1; i <= cuerpo.length; i++) {
          // Obtener su Producto con el Múltiplo Correspondiente
            var index = multiplo * valor.charAt(cuerpo.length - i)

            // Sumar al Contador General
            suma = suma + index

            // Consolidar Múltiplo dentro del rango [2,7]
            if (multiplo < 7) { multiplo = multiplo + 1 } else { multiplo = 2 }
          }
          // Calcular Dígito Verificador en base al Módulo 11
          var dvEsperado = 11 - (suma % 11)
          // Casos Especiales (0 y K)
          dv = (dv === 'K') ? 10 : dv
          dv = (dv === 0) ? 11 : dv
          // Validar que el Cuerpo coincide con su Dígito Verificador

          // console.log('dv:' + dv + 'dvEsperado:' + dvEsperado)
          if (parseInt(dvEsperado) !== parseInt(dv)) { this.rutValidado = false } else {
            this.rutValidado = true
          }
        }
        // Calcular Dígito Verificador
      },
      onChangeSelect (val) {
        this.editedItem.id_item.nombre = val.target.value
      },
      onChangeSelectAgregar (val) {
        this.selectJustificacion = true
        if (this.itemMermaSelectID !== null) {
          this.selectValidado = true
        } else {
          this.selectValidado = false
        }
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/merma/`) // petición GET a Merma para traer a todos los objetos "Merma"
          .then((response) => {
            this.items = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectItem () {
        axios.get(config.API_LOCATION + `/bodega/item/`) // petición GET a Item para traer a todos los objetos "items"que contengan como tipo
          .then((response) => {
            this.itemMerma = response.data
            this.itemMermaSelectID = this.itemMerma
          })
          .catch(e => {
          })
      },
      cargarStock (id, cantidad) {
        var stock
        var stockFinal
        axios.get(config.API_LOCATION + `/bodega/item/` + id + '')
        // petición GET a Item para traer a todos los objetos "items"que contengan como tipo
          .then((responseGetItem) => {
            stock = responseGetItem.data.stock
            stockFinal = parseInt(stock) - parseInt(cantidad)
            axios.put(config.API_LOCATION + `/bodega/item/` + id + '', {
              // peticion PUT para editar el stock al momento de realizar una merma
              nombre: responseGetItem.data.nombre,
              id_marca: {id: responseGetItem.data.id_marca.id},
              modelo: responseGetItem.data.modelo,
              stock: stockFinal,
              stockCritico: responseGetItem.data.stockCritico,
              id_unidad_medida: { id: responseGetItem.data.id_unidad_medida.id },
              id_subcategoria: { id: responseGetItem.data.id_subcategoria.id }})
            // console.log('nombre:' + responseGetItem.data.nombre + '**' + 'marca' + responseGetItem.data.marca + 'modelo:' + responseGetItem.data.modelo + '**' + 'stock' + stockFinal + 'idUnidadMedida:' + { id: responseGetItem.data.id_unidad_medida.id } + '**' + 'id_subcategoria:' + { id: responseGetItem.data.id_subcategoria.id })
              .then((response) => {
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
      },
      cargarStockEdit (id, cantidadInicial, cantidadFinal) {
        var stock
        var stockFinal
        axios.get(config.API_LOCATION + `/bodega/item/` + id + '')
        // petición GET a Item para traer a todos los objetos "items"que contengan como tipo
          .then((responseGetItem) => {
            stock = responseGetItem.data.stock
            stockFinal = (parseInt(stock) + parseInt(cantidadInicial)) - parseInt(cantidadFinal)
            console.log(cantidadInicial + 'inicial ' + cantidadFinal + 'final ' + stock)
            axios.put(config.API_LOCATION + `/bodega/item/` + id + '', {
              // peticion PUT para editar el stock al momento de realizar una merma
              nombre: responseGetItem.data.nombre,
              id_marca: {id: responseGetItem.data.id_marca.id},
              modelo: responseGetItem.data.modelo,
              stock: stockFinal,
              stockCritico: responseGetItem.data.stockCritico,
              id_unidad_medida: { id: responseGetItem.data.id_unidad_medida.id },
              id_subcategoria: { id: responseGetItem.data.id_subcategoria.id }})
              .then((response) => {
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
      },
      agregarMerma (e) { // función para agregar un nuevo Merma
        if (this.selectJustificacion === true) {
          this.selectValidado = true
        } else {
          this.selectValidado = false
        }
        var motivo = e.target.elements.motivoMerma.value
        var responsable = e.target.elements.responsableMerma.value
        var idItem = this.itemMermaSelectID.id
        var cantidad = e.target.elements.cantidadMerma.value
        if (this.$refs.fAgregarMerma.validate()) {
          axios.post(config.API_LOCATION + '/bodega/merma/', { // petición POST a Merma para agregar
            fecha: '',
            motivo: '' + motivo + '',
            responsable: '' + responsable + '',
            id_item: {id: '' + idItem + ''},
            cantidad: '' + cantidad + ''
          })
            .then((response) => {
              this.cargarStock(idItem, cantidad)
              this.initialize() // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.snackbar = true
              this.$refs.fAgregarMerma.reset()
              this.rutValidado = false
              this.selectValidado = true
              this.selectJustificacion = false
              this.cargarSelectItem()
            })
        }
      },
      modalEdit (item) { // funcion para llenar los items
        this.cantidadInicialEdit = item.cantidad
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialogEdit = true // abrir modal edit
      },
      editMerma (e) { // función para editar el Merma
        var id = e.target.elements.idEdit.value // obtener id del objeto que se desea editar formulario
        var fecha = e.target.elements.fechaEdit.value // obtener fecha del objeto que se desea editar formulario
        var motivo = e.target.elements.motivoEdit.value // obtener motivo del objeto que se desea editar formulario
        var responsable = e.target.elements.responsableEdit.value // obtener responsable del objeto que se desea editar formulario
        var idItem = this.editedItem.id_item.id
        var cantidad = e.target.elements.cantidadEdit.value // obtener cantidad del objeto que se desea editar formulario
        var cantidadInicial = this.cantidadInicialEdit
        if (this.$refs.fEditarMerma.validate()) {
          axios.put(config.API_LOCATION + '/bodega/merma/' + id + '', {// petición put para editar el Merma
            fecha: '' + fecha + '',
            motivo: '' + motivo + '',
            responsable: '' + responsable + '',
            id_item: {id: '' + idItem + ''},
            cantidad: '' + cantidad + ''
          })
            .then(response => {
              Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
              this.cargarStockEdit(idItem, cantidadInicial, cantidad)
              this.dialogEdit = false // cerrar modal
            })
            .catch(function (error) {
              console.log(error)
            })
        }
      },
      modalDelete (item) { // función para editar el Merma
        this.deleteIndex = this.items.indexOf(item) // obtener posición del array
        this.deleteItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteMerma () { // función para eliminar un Merma
        var id = this.deleteItem.id // id del objeto a eliminar
        console.log(this.items.length)
        axios.delete(config.API_LOCATION + '/bodega/merma/' + id + '') // petición DELETE a Merma para poder eliminar un Merma
          .then(response => {
            this.items.splice(this.deleteIndex, 1)
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
        // this.$refs.fAgregarMerma.validate()
        // this.$refs.fAgregarMerma.shouldValidate = false
        this.$refs.fAgregarMerma.reset()
        this.rutValidado = false
        this.selectValidado = true
        this.selectJustificacion = false
        this.cargarSelectItem()
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
