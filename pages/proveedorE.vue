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
    <!-- Dialog Eliminar Tipo -->
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
            <v-card-text class="px-0" v-model="deleteItem.nombre" style="text-align:center;">Nombre : {{deleteItem.nombre}}</v-card-text>
          </v-card>
        </v-flex>

            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialogDelete = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="deleteInsumo">Eliminar</v-btn>

        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- Termino Dialog Eliminar Tipo-->
  <!-- Dialog Agregar Tipo -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Proveedor</v-btn>
            <v-form @submit.prevent="agregarProveedorN" v-model="valid" ref="fAgregarProveedorE" lazy-validation>
      <v-card>
        <v-card-title><h1> Nuevo Proveedor</h1></v-card-title>
          <v-divider></v-divider>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
                <v-flex xs12>
                <v-text-field label="Rut"  :rules="[v => !!v || 'Campo vacío', v => this.rutValidado || 'Rut Invalido']" v-on:keyup="checkRut" :counter="20" name="rutProveedorN" v-model="addItem.rut" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                <v-text-field label="Nombre"  :rules="textoRules" :counter="20" name="nombreProveedorN" v-model="addItem.nombre" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                <v-text-field label="Representante" :rules="textoRules" :counter="20" name="representanteProveedorN" v-model="addItem.representante" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                <v-text-field label="Dirección" :rules="textoRules" :counter="20" name="direccionProveedorN" v-model="addItem.direccion" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                <v-text-field label="Email" :rules="emailRules" :counter="20" name="emailProveedorN" v-model="addItem.email" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                 <v-text-field label="Telefono" :rules="textoRules" :counter="20" name="fonoProveedorN" v-model="addItem.fono" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                 <v-text-field label="Movil" :rules="textoRules" :counter="20" name="movilProveedorN" v-model="addItem.movil" required></v-text-field>
                </v-flex>
                <v-flex xs12>
                <v-select item-value="id" item-text="nombre" :items="ciudadItem" v-model="addItem.id_ciudad.id" label="Ciudad" single-line :rules="[v => this.selectValidado || 'Campo vacío']" v-on:change='onChangeCiudad'
                ></v-select>
                </v-flex>
                <v-flex xs12 sm6 md12>
                <v-select
                item-value="id"
                item-text="nombre"
                :items="giroItem"
                :value="addItem.id_giro"
                v-model="addItem.id_giro"
                label="Giro"
                :rules="[v => this.selectValidado2 || 'Campo vacío']"
                v-on:change='onChangeGiro'
                single-line
                ></v-select>
                </v-flex>
                <v-flex xs12 id="divItems">
                <item x12 @clicked="onClickChild" @deleted="itemEliminado"></item>
                </v-flex>
                </v-flex>
                <v-flex xs12>
                </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-divider></v-divider>
          <v-btn color="blue darken-1" flat @click="clearAddModal">Cancelar</v-btn>
          <v-btn color="blue darken-1" type="submit" flat >Guardar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Agregar Tipo -->
    <!-- Dialog Editar Tipo -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <form @submit.prevent="editProveedor">
      <v-card>
          <v-card-title><h1> Editar Proovedor </h1></v-card-title>
          <v-divider></v-divider>        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 style="display:none;">
                <v-text-field label="rut" v-model="editedItem.rut" name="rutEdit" ></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field label="Nombre" :counter="20" v-model="editedItem.nombre" name="nombreEdit" :rules="textoRules"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field label="Representante" :counter="20" v-model="editedItem.representante" :rules="textoRules" name="representanteEdit"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field label="Direccion" :counter="20" :rules="textoRules" v-model="editedItem.direccion" name="direccionEdit"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field label="Email" :counter="20" :rules="emailRules" v-model="editedItem.email" name="emailEdit"></v-text-field>
              </v-flex>
               <v-flex xs6>
                <v-text-field label="Movil" :counter="20" :rules="textoRules" v-model="editedItem.movil" name="movilEdit"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field label="Teléfono" :counter="20" :rules="textoRules" v-model="editedItem.fono" name="fonoEdit"></v-text-field>
              </v-flex>
              <h4>Ciudad:</h4>
              <v-flex xs12>
                <v-select
                id="selectCiudadEdit"
                :items="ciudadItem"
                item-text="nombre"
                item-value="id"
                @select='onChangeSelect'
                v-model="editedItem.id_ciudad.id"
                search-input autocomplete label="Ciudad"
                single-line>
                </v-select>
        </v-flex>
        <v-flex xs9>
              <h4>Giros:</h4>
        </v-flex>
                <v-flex xs3>
             <v-btn small color="primary" @click="modalEdit1(editedItem)" dark>Ver Giros</v-btn>
        </v-flex>
        </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-divider></v-divider>
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
       <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>
          <v-card-title><h1> Detalle de Proveedor Extranjero </h1></v-card-title>
          <v-divider></v-divider>

          <v-list dense >
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre Proveedor</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.nombre }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Rut Proveedor</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.rut }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Representante</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.representante }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Dirección</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.direccion }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Email</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.email }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Teléfono</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fono }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Móvil</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.movil }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Ciudad</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id_ciudad.nombre }}</v-list-tile-title>
            </v-list-tile>

             <v-flex xs12>
             <v-expansion-panel focusable>
           <v-expansion-panel-content v-for="(item,i) in 1" :key="i">
             <div slot="header"><h4>Giro/s :</h4></div>
             <v-card>
               <v-card-text class="light-green lighten-3" v-for="giro in giros">{{giro.id_giro.id}} : {{ giro.id_giro.nombre }}</v-card-text>
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


    <v-dialog v-model="dialog3" max-width="500px">
            <v-form @submit.prevent="agregarMarca" v-model="valid" ref="fagregarMarca" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Editar o Eliminar Giros</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            <v-flex xs12 sm12 md12>
                <h3>Giros Actuales:</h3>
              </v-flex>

                 <v-flex xs12 sm12>
            <div v-for="giro in giros" :name="giros.id_giro" class="divItem">
                 <v-flex xs12 sm12>
                 <v-select
                id="selectGiroEdit"
                :items="giroItem"
                item-text="nombre"
                item-value="id"
                :value="giro.id_giro.id"
                v-model="giro.id_giro.id"
                label="Giro"
                readonly>
                </v-select>
                </v-flex>
                <v-flex >
                <v-btn dark color="error" class="deleteBtn" @click="eliminarItem1(giro.id)">X</v-btn>
               </v-flex>

             </div>
              </v-flex>
                <v-flex xs12 sm12 md12>
                <h3>Agregar</h3>
              </v-flex>
                <v-flex xs12 sm12 md10>
                <v-select
                id="selectItemGiro"
                item-value="id"
                item-text="nombre"
                :items="giroItem"
                :value="addItem.id_giro"
                v-model="addItem.id_giro"
                label="Giro"
                single-line
                ></v-select>
                </v-flex>
                <v-flex xs12 sm12 md2>
                <v-btn fab small dark @click="agregadetalle1(editedItem.rut)">
                <v-icon dark>add</v-icon>
                </v-btn>

              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="dialog3=false">Cancelar</v-btn>
        </v-card-actions>
      </v-card>
      </v-form>
    </v-dialog>
    <!-- Fin Dialog Editar Tipo -->
    <!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>LISTA DE PROVEEDORES EXTRANJEROS</h1>
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
        <td class="text-xs-center">{{ props.item.rut }}</td>
        <td class="text-xs-center">{{ props.item.nombre }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.representante }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.direccion }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.id_ciudad.nombre }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.fono }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.movil }}</td>
        <td class="text-xs-center" style="display:none;" >{{ props.item.email }}</td>
         <td class="text-xs-center"style="display:none;" >{{ props.item.nacional }}</td>
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
  import item from '../components/proveedor/item.vue'
  export default {
    components: { config, item },
    data: () => ({
      ciudad: 0,
      ciudadItem: [],
      giroItem: [],
      textoRules: validaciones.textoRules,
      emailRules: validaciones.emailRules,
      selectValidado: false,
      selectValidado2: false,
      dialogAdd: false, // prop para abrir y cerrar modal de Agregar Tipo
      dialogEdit: false, // prop para abrir y cerrar modal de Editar Tipo
      dialogDetail: false, // prop para abrir y cerrar modal de Detalle Tipo
      dialogDelete: false,
      dialog3: false, // prop para abrir y cerrar modal de Delete Tipo
      rutValidado: false,
      pagination: {}, // paginación de la tabla
      editedIndex: -1,
      deleteIndex: -1,
      search: '',
      selectSelectGiro: 0,
      inputArray: [{idInput: 'selectItemGiro'}],
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      headers: [ // Encabezados de  la tabla
        {
          text: 'Rut',
          value: 'nombre',
          sortable: true,
          width: '33%',
          align: 'center'
        },
        { text: 'Nombre', value: 'rut', width: '33%', align: 'center' },
        { text: 'Opciones', sortable: false, width: '33%', align: 'center' }
      ],
      items: [],
      giros: [],
      valid: true,
      addItem: {
        rut: '',
        nombre: '',
        representante: '',
        direccion: '',
        email: '',
        fono: '',
        movil: '',
        id_ciudad: {id: 0},
        id_giro: 0
      },
      editedItem: { // prop temporal que guarda el objeto a editar o eliminar
        rut: '',
        nombre: '',
        representante: '',
        direccion: '',
        email: '',
        fono: '',
        movil: '',
        id_ciudad: {id: 0}
      },
      detailItem: {
        rut: '',
        nombre: '',
        representante: '',
        direccion: '',
        email: '',
        fono: '',
        movil: '',
        id_ciudad: {id: 0}
      },
      detailItem1: [],
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
      this.cargarSelectCiudad()
      this.cargarSelectGiro()
    },
    methods: {
      onChangeSelect (val) {
        console.log(val)
        this.editedItem.id_ciudad.nombre = val.target.value
      },
      onChangeCiudad (val) {
        this.selectValidado = true
      },
      onChangeGiro (val) {
        this.selectValidado2 = true
      },
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
      itemEliminado (e) {
        console.log(e)
        var indexArray = this.inputArray.findIndex(x => x.idInput === e)
        this.inputArray.splice(indexArray, 1)
        console.log(this.inputArray)
      },
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + `/bodega/provNaciFalse/`) // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.items = response.data// LLenado del array "items"
          })
          .catch(e => {
          })
      },
      cargarSelectCiudad () {
        axios.get(config.API_LOCATION + `/bodega/ciudad/`) // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.ciudadItem = response.data
          })
          .catch(e => {
          })
      },
      cargarSelectGiro () {
        axios.get(config.API_LOCATION + `/bodega/giro/`)// petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.giroItem = response.data
          })
          .catch(e => {
          })
      },
      selectItemGiro (val) {
        this.addItem.stock = val.stock
        this.cambioLabel1(val.stock)
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
      eliminarItem (e) {
        console.log(e.path)
        var nameDiv = e.path[2].getAttribute('name')
        this.$emit('deleted', nameDiv)
        e.path[1].parentNode.remove()
      },
      eliminarItem1 (e) {
        var id = e// id del objeto a eliminar
        axios.delete(config.API_LOCATION + '/bodega/detalle_giro/' + id + '') // petición DELETE a tipo para poder eliminar un tipo
          .then(response => {
            this.items.splice(this.deleteIndex, 1)
            console.log(this.items.length)
            this.dialogDelete = false// cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      agregarItemPrestado () {
        var html1 = '<item></item>'
        var z = document.createElement('item')
        z.innerHTML = html1
        document.body.appendChild(z)
        document.getElementById('divItems').insertAdjacentHTML('afterend', html1)
      },
      onClickChild (value) {
        this.inputArray.push(value)
        console.log(this.inputArray) // someValue
      },
      agregarProveedorN (e) { // función para agregar un nuevo Tipo
        var rut = this.addItem.rut
        console.log(rut)
        var nombre = this.addItem.nombre
        console.log(nombre)
        var representante = this.addItem.representante
        console.log(representante)
        var direccion = this.addItem.direccion
        console.log(direccion)
        var email = this.addItem.email
        console.log(email)
        var fono = this.addItem.fono
        console.log(fono)
        var movil = this.addItem.movil
        console.log(movil)
        var ciudad = this.addItem.id_ciudad.id
        console.log(ciudad)
        var nacional = false
        if (this.$refs.fAgregarProveedorE.validate()) {
          axios.post(config.API_LOCATION + '/bodega/proveedor/', { // petición POST a Tipo para agregar
            rut: '' + rut + '',
            nombre: '' + nombre + '',
            representante: '' + representante + '',
            direccion: '' + direccion + '',
            email: '' + email + '',
            fono: '' + fono + '',
            movil: '' + movil + '',
            nacional: '' + nacional + '',
            id_ciudad: { id: ciudad }

          }
          )
            .then((response) => {
              this.agregadetalle(response.data.rut)
              this.initialize() // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.snackbar = true
              this.selectValidado = false
              this.selectValidado2 = false
              this.rutValidado = false
            })
        }
      },
      agregadetalle (rutProveedor) {
        var rut = rutProveedor
        for (var i = 0; i < this.inputArray.length; i++) {
          console.log('entre')
          // console.log((this.inputArray[i].idInput).getAttribute('value'))
          var idgiro = parseInt(document.getElementById(this.inputArray[i].idInput).getAttribute('value'))
          console.log(document.getElementById(this.inputArray[i].idInput))
          console.log(idgiro)
          console.log(rut)
          axios.post(config.API_LOCATION + '/bodega/detalle_giro/', { // petición POST a Tipo para agregar
            id_giro: {id: idgiro},
            rut_proveedor: {rut: rut}
          }
          )
            .then((response) => {
              this.initialize() // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.snackbar = true
            })
        }
      },
      agregadetalle1 (rutProveedor) {
        var rut = this.editedItem.rut
        var idgiro = this.addItem.id_giro
        console.log(idgiro)
        console.log(rut)
        axios.post(config.API_LOCATION + '/bodega/detalle_giro/', { // petición POST a Tipo para agregar
          id_giro: {id: idgiro},
          rut_proveedor: {rut: rut}
        }
        )
          .then((response) => {
            this.initialize() // push al array de items
            this.dialogAdd = false // cerrar el modal
            this.snackbar = true
            this.dialog3 = false
          })
      },
      agregadetalleeditar (rutProveedor) {
        var rut = rutProveedor
        for (var i = 0; i < this.inputArray.length; i++) {
          console.log('entre')
          // console.log((this.inputArray[i].idInput).getAttribute('value'))
          var idgiro = parseInt(document.getElementById(this.inputArray[i].idInput).getAttribute('value'))
          console.log(document.getElementById(this.inputArray[i].idInput))
          console.log(idgiro)
          console.log(rut)
          axios.post(config.API_LOCATION + '/bodega/detalle_giro/', { // petición POST a Tipo para agregar
            id_giro: {id: idgiro},
            rut_proveedor: {rut: rut}
          }
          )
            .then((response) => {
              this.initialize() // push al array de items
              this.dialogAdd = false // cerrar el modal
              this.snackbar = true
            })
        }
      },
      editProveedor (e) { // función para editar la Subcategoría
        var rut = this.editedItem.rut// obtener id del objeto que se desea editar formulario
        var nombre = this.editedItem.nombre// obtener nombre del objeto que se desea editar
        console.log(nombre)
        console.log(nombre)
        var representante = this.editedItem.representante
        console.log(representante)
        var direccion = this.editedItem.direccion
        console.log(direccion)
        var email = this.editedItem.email
        console.log(email)
        var fono = this.editedItem.fono
        console.log(fono)
        var movil = this.editedItem.movil
        console.log(movil)
        var ciudad = this.editedItem.id_ciudad.id
        console.log(ciudad)
        var nacional = false
        axios.put(config.API_LOCATION + '/bodega/proveedor/' + rut + '', {// petición put para editar el tipo
          nombre: '' + nombre + '',
          representante: '' + representante + '',
          direccion: '' + direccion + '',
          email: '' + email + '',
          fono: '' + fono + '',
          movil: '' + movil + '',
          nacional: '' + nacional + '',
          id_ciudad: { id: ciudad }
        }
        )
          .then(response => {
            Object.assign(this.items[this.editedIndex], this.editedItem)
            this.snackbar = true // eliminar objeto del array items
            this.dialogEdit = false // cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      editGiro (e) { // función para editar la Subcategoría
        axios.put(config.API_LOCATION + '/bodega/detalle_giro/', {// petición put para editar el tip
        }
        )
          .then(response => {
            Object.assign(this.items[this.editedIndex], this.editedItem) // eliminar objeto del array items
            this.dialogEdit = false // cerrar modal
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      modalEdit (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.traerdetalle(item)
        this.dialogEdit = true // abrir modal edit
      },
      modalEdit1 (item) { // funcion para llenar los items
        this.editedIndex = this.items.indexOf(item) // obtener posición del array
        this.editedItem = Object.assign({}, item)
        this.dialog3 = true // abrir modal edit
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
        this.$refs.fAgregarProveedorE.reset()
        this.dialogAdd = false
        this.selectValidado = false
        this.selectValidado2 = false
        this.rutValidado = false
        this.dialog3 = false
      },
      clear () {
        // this.$refs.fAgregarProveedorE.reset()
        this.dialogAdd = true
        this.dialog3 = false
      },
      clearEditModal () {
        this.dialogEdit = false
      },
      modalDetalle (item) {
        this.detailItem = this.items.indexOf(item)
        this.traerdetalle(item)
        this.detailItem1 = this.items.indexOf(item)// obtener posición del array
        this.detailItem = Object.assign({}, item)
        this.dialogDetail = true
      },
      cerrarModalDetail () {
        this.dialogDetail = false
      },
      traerdetalle (item) {
        axios.get(config.API_LOCATION + '/bodega/detalle_giro/' + item.rut + '/proveedor') // petición GET a Categoria para traer a todos los objetos "categoria"que contengan como tipo "insumo"
          .then((response) => {
            this.giros = []
            this.giros = response.data// LLenado del array "items"
            console.log(this.giros)
          })
          .catch(e => {
          })
      }
    }
  }
</script>
<style type="text/css">
  .hoverMouse:hover{
    background-color: #D5CFCF;
  }
</style>
