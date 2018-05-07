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
            <v-btn color="primary" dark slot="activator" @click="pruebas">Prueba</v-btn>
    <v-layout >
      <v-dialog v-model="dialogAdd" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-btn color="primary" dark slot="activator">Crear nuevo Contrato</v-btn>

        <v-card>
           <v-form @submit.prevent="agregarContrato" v-model="valid" ref="fAgregarHerramientas" lazy-validation>
          <v-toolbar dark color="primary">
            <v-btn icon @click.native="cerrarModalAgregar" dark>
              <v-icon>close</v-icon>
            </v-btn>
            <!--<v-toolbar-title>Settings</v-toolbar-title>-->
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn dark flat type="submit">Guardar</v-btn>
            </v-toolbar-items>
          </v-toolbar>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-stepper v-model="e1" non-linear>
                  <v-stepper-header>
                    <v-stepper-step step="1" :complete="e1 > 1" editable >Empresa</v-stepper-step>
                    <v-divider></v-divider>
                    <v-stepper-step step="2" :complete="e1 > 2" editable >Administrador</v-stepper-step>
                    <v-divider></v-divider>
                    <v-stepper-step step="3" :complete="e1 > 3" editable >Servicio</v-stepper-step>
                    <v-divider></v-divider>
                    <v-stepper-step step="4" :complete="e1 > 4" editable >Fecha de Pago</v-stepper-step>
                    <v-divider></v-divider>
                    <v-stepper-step step="5" :complete="e1 > 5" editable >Vista Previa</v-stepper-step>
                  </v-stepper-header>
                  <v-stepper-items>
                    <v-stepper-content step="1">
                      <v-card  class="mb-5">
                        <v-container grid-list-md>
                          <v-layout wrap>
                            <v-flex xs12>
                            <p><strong>Empresa</strong></p>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="RUT" v-model="rutEmpresa" id="rutEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Nombre" v-model="nombreEmpresa" id="nombreEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Fono" v-model="fonoEmpresa" id="fonoEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Dirección" v-model="direccionEmpresa" id="direccionEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs12>
                              <v-divider></v-divider>
                               <p><strong>Datos Contacto</strong></p>
                            </v-flex>
                             <v-flex xs6>
                              <v-text-field label="Nombre" id="nombreContactoEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="E-mail" id="emailContactoEmpresa"></v-text-field>
                            </v-flex>
                            <v-flex xs12>
                              <v-divider></v-divider>
                               <p><strong>Configuración Empresa</strong></p>
                            </v-flex>
                            <v-flex xs6>
                              <v-flex xs12>
                              
                               <p><strong>Color Primario</strong></p>
                            </v-flex>
                               <no-ssr>
                                 <swatches-picker v-model="colorPrimario"></swatches-picker>
                               </no-ssr>
                            </v-flex>
                            <v-flex xs6>
                              <v-flex xs12>
                              
                               <p><strong>Color Secundario</strong></p>
                            </v-flex>
                               <no-ssr>
                                 <swatches-picker v-model="colorSecundario"></swatches-picker>
                               </no-ssr>
                            </v-flex>
                            <v-flex xs6>
                             <p><strong>Logo 1</strong></p>
                            <input type="file" accept="image/*"></input>
                          </v-flex>
                          <v-flex xs6>
                             <p><strong>Logo 2</strong></p>
                            <input type="file" accept="image/*" @change="changePrueba"></input>
                          </v-flex>
                          </v-layout>
                        </v-container>
                      </v-card>
                      <v-btn color="primary" @click.native="e1 = 2">Continuar</v-btn>
                    </v-stepper-content>
                    <v-stepper-content step="2">
                      <v-card class="mb-5">
                        <v-container grid-list-md>
                          <v-layout wrap>
                            <v-flex xs4>
                              <v-text-field label="RUT Administrador" id="rutAdministrador"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Primer Nombre" id="primerNombre"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Segundo Nombre" id="segundoNombre"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Primer Apellido" id="primerApellido"></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                              <v-text-field label="Segundo Apellido" id="segundoApellido"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Contraseña" id="contraseña"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Repetir Contraseña" id="confirmarContraseña"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Dirección Funcionario" id="direccionFuncionario"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Ciudad" id="ciudad"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Fono" id="fonoFuncionario"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="Movil" id="movilFuncionario"></v-text-field>
                            </v-flex>
                            <v-flex xs6>
                              <v-text-field label="E-Mail" id="emailFuncionario"></v-text-field>
                            </v-flex>
                            <!--
                                
                             <v-flex xs6>
                              <v-text-field label="Cargo"></v-text-field>
                            </v-flex>
                          -->
                          </v-layout>
                        </v-container>
                      </v-card>
                      <v-btn flat @click.native="e1 = e1 - 1">Atrás</v-btn>
                      <v-btn color="primary" @click.native="e1 = 3">Continuar</v-btn>
                    </v-stepper-content>
                    <v-stepper-content step="3">
                      <v-card class="mb-5" >
                        <v-container grid-list-md>
                          <v-layout wrap>
                        <v-flex xs6>
                           <v-card-title>
                            <h1>Servicios</h1>
                            <v-spacer></v-spacer>
                            <v-text-field
                              append-icon="search"
                              label="Buscar"
                              single-line
                              hide-details
                              v-model="search1"
                            ></v-text-field>
                          </v-card-title>
                          <v-data-table
                            :headers="headersTablaServicios"
                            :items="servicios"
                            :search="search1"
                            must-sort
                            :pagination.sync="paginationServicios"
                            class="elevation-1"
                          >
                            <template @click="prueba" slot="items" slot-scope="props" color="green">
                              <td class="text-xs-center" @click="prueba" >{{ props.item.id }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.id_servicio.nombre }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.id_servicio.precio }}</td>
                              <td class="justify-center layout px-0">
                              <v-tooltip top>
                                <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
                                  <v-icon color="blue">search</v-icon>
                                </v-btn>
                                <span>Detalle</span>
                                </v-tooltip>
                                <v-tooltip top>
                                <v-btn icon slot="activator" class="mx-0" @click="agregarCarro(props.item)" >
                                  <v-icon color="green">add_shopping_cart</v-icon>
                                </v-btn>
                                <span>Añadir</span>
                                </v-tooltip>
                              </td>
                            </template>
                            <template slot="pageText" slot-scope="{ pageStart, pageStop }">
                              De {{ pageStart }} a {{ pageStop }}
                            </template>
                          </v-data-table>
                        </v-flex>
                         <v-flex xs6>
                           <v-card-title>
                             <v-icon color="green">monetization_on</v-icon> <h2>  {{ totalServicio }}</h2>
                            <v-spacer></v-spacer>
                            <v-text-field
                              append-icon="search"
                              label="Buscar"
                              single-line
                              hide-details
                              v-model="search2"
                            ></v-text-field>
                          </v-card-title>
                          <v-data-table
                            :headers="headersTablaServicios"
                            :items="serviciosCarro"
                            :search="search2"
                            must-sort
                            :pagination.sync="paginationServicios"
                            class="elevation-1"
                          >
                            <template @click="prueba" slot="items" slot-scope="props" color="green">
                              <td class="text-xs-center" @click="prueba" >{{ props.item.id }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.id_servicio.nombre }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.id_servicio.precio }}</td>
                              <td class="justify-center layout px-0">
                              <v-tooltip top>
                                <v-btn icon slot="activator" class="mx-0" @click="modalDetalle(props.item)" >
                                  <v-icon color="blue">search</v-icon>
                                </v-btn>
                                <span>Detalle</span>
                                </v-tooltip>
                                <v-tooltip top>
                                <v-btn icon slot="activator" class="mx-0" @click="quitarCarro(props.item)" >
                                  <v-icon color="red">remove_shopping_cart</v-icon>
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
                      </v-layout>
                    </v-container>
                      </v-card>
                      <v-btn flat @click.native="e1 = e1 - 1">Atrás</v-btn>
                      <v-btn color="primary" @click.native="e1 = 1">Continuar</v-btn>
                    </v-stepper-content>
                    <v-stepper-content step="4">
                      <v-card class="mb-5">
                        <v-flex xs12>
                          <v-date-picker v-model="picker" :landscape="landscape" :reactive="reactive"></v-date-picker>
                        </v-flex>
                      </v-card>
                      <v-btn flat @click.native="e1 = e1 - 1">Atrás</v-btn>
                      <v-btn color="primary" @click.native="e1 = 1">Continuar</v-btn>
                    </v-stepper-content>
                    <v-stepper-content step="5">
                      <v-card class="mb-5">
                        <h1>{{ nombreEmpresa }}</h1>
                        <h1>{{ rutEmpresa }}</h1>
                        <h1>{{ fonoEmpresa }}</h1>
                        <h1>{{ direccionEmpresa }}</h1>
                      </v-card>
                      <v-btn flat @click.native="e1 = e1 - 1">Atrás</v-btn>
                      <!--<v-btn color="primary" @click.native="e1 = 1">Finalizar</v-btn>-->
                    </v-stepper-content>
                  </v-stepper-items>
                </v-stepper>
              </v-flex>

            </v-layout>
          </v-container>
        </v-form>
        </v-card>
      </v-dialog>
    </v-layout>
 		 <!-- Dialog Agregar Funcion -->
   
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
        <h1>Contratos</h1>
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
        <td class="text-xs-center">{{ props.item.id_modulo.nombre }}</td>
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
import { Swatches } from 'vue-color'
	export default {
     components: {
      'swatches-picker': Swatches
    },
    layout: 'usuarioTemplate',
		data: () => ({
			headers: [
		        { text: 'ID', value: 'rut', sortable: true, width: '25%', align: 'center' },
		        { text: 'Nombre', value: 'nombre', width: '25%', align: 'center' },
            { text: 'Módulo', value: 'modulo', width: '25%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
		    ],
        headersTablaServicios: [
            { text: 'ID', value: 'rut', sortable: true, width: '25%', align: 'center' },
            { text: 'Nombre', value: 'nombre', width: '25%', align: 'center' },
            { text: 'Precio', value: 'precio', width: '25%', align: 'center' },
            { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
        ],
      funciones: [],
			modulos: [],
      servicios: [],
      serviciosCarro: [],
      // Date Picker
      picker: null,
      landscape: true,
      reactive: false,
      // Fin Date Picker
      tablas: [{tabla_main: 'Funcionario'}],
      colorPrimario: {hex: ''},
      colorSecundario: {hex: ''},
			pagination: {},
      paginationServicios: {},
      totalServicio: 0,
      search: '',
			search1: '',
      search2: '',
			valid: true,
      imagen: '',
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
      dialogDelete: false,
      dialogMantencion: false,
      selectModulo: 0,
      selectModuloEdit: {id: 0, nombre: ''},
      selectTablaEdit: 0,
      e1: 0,
      selectTabla: '',
      // SnackBar
      snackbar: false,
      color: 'green',
      mode: '',
      timeout: 3000,
      text: 'Se ha agregado con exito',
      //Modal Agregar
      notifications: false,
      sound: true,
      widgets: false,
      // Atributos contrato "Paso 1"
      rutEmpresa: '',
      nombreEmpresa: '',
      fonoEmpresa: '',
      direccionEmpresa: '',

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
      // this.initialize()
      this.cargarModulos()
      this.cargarServicios()
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/contrato/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.funciones = response.data
          })
          .catch(e => {
          })
      },
      changePrueba (e) {
        console.log(e.target.value)
      },
      pruebas () {
        console.log(this.colorPrimario)
        console.log(this.colorSecundario)
        console.log(this.imagen)
      },
      cargarModulos () {
        axios.get(config.API_LOCATION + '/user/modulo/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.modulos = response.data
          })
          .catch(e => {
          })
      },
      cargarServicios () {
        axios.get(config.API_LOCATION + '/user/d_servicio/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.servicios = response.data
          })
          .catch(e => {
          })
      },
			agregarContrato (e) {
        console.log(e)

        // E_Cliente
        var rut = document.getElementById('rutEmpresa').value
        var nombre = document.getElementById('nombreEmpresa').value
        var fono = document.getElementById('fonoEmpresa').value
        var direccion = document.getElementById('direccionEmpresa').value
        var contacto = document.getElementById('nombreContactoEmpresa').value
        var email = document.getElementById('emailContactoEmpresa').value
        

        // Funcionario
        var rutFuncionario = document.getElementById('rutAdministrador').value
        var nombreFuncionario1 = document.getElementById('primerNombre').value
        var nombreFuncionario2 = document.getElementById('segundoNombre').value
        var apellido_p = document.getElementById('primerApellido').value
        var apellido_m = document.getElementById('segundoApellido').value
        var direccionFuncionario = document.getElementById('direccionFuncionario').value
        var ciudad = document.getElementById('ciudad').value
        var fono = document.getElementById('fonoFuncionario').value
        var movil = document.getElementById('movilFuncionario').value
        var emailFuncionario = document.getElementById('emailFuncionario').value
        var password = document.getElementById('confirmarContraseña').value
        // var e-cliente = 

        // conf cliente
        
        var color_p = this.colorPrimario.hex
        var color_s = this.colorSecundario.hex
        var logo_1 =  ''
        var logo_2 =  ''

       
        axios.post(config.API_LOCATION + '/user/e_cliente/', { //POST a Empresa Cliente
          rut: rut,
          nombre: nombre,
          fono: fono,
          direccion: direccion,
          contacto: contacto,
          email: email
         })
          .then((response) => {
            /*
            calculo valor membresia
            var valor_memb = 0;
             for (var i = 0; i < this.serviciosCarro.length; i++){
              valor_memb = 
             }
             */
             axios.post(config.API_LOCATION + '/user/contrato/', { // POST a contrato 
              id_e_cliente: {rut: response.data.rut},
              valor_memb: 1000,
              fecha: '',
              estado: true,
              
             }) 
             .then((response) => {

              for (var i = 0; i < this.serviciosCarro.length ; i++) {
                axios.post(config.API_LOCATION + '/user/anexo/', { // POST a anexo 
                  id_contrato: {id: response.data.id},
                  id_servicio: {id: this.serviciosCarro[i].id_servicio.id},
                  fecha_inicio: '',
                  fecha_termino: '',
                  fecha_termino: '',
                  
                 }) 
                 .then((responseAnexo) => {
                  console.log('entre responseAnexo')
                  console.log(responseAnexo)
                  axios.post(config.API_LOCATION + '/user/cupo/', { // POST a configuracion Cliente 
                    id_anexo: {id: responseAnexo.data.id},
                    id_d_servicio: {id: this.serviciosCarro[i].id},
                    lleno: false
                  }) 
                  .then((responseCupo) => {
                    console.log('entre responseCupo')
                  console.log(responseCupo)
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
            
            axios.post(config.API_LOCATION + '/user/conf_e_cliente/', { // POST a configuracion Cliente 
              color_p: color_p,
              color_s: color_s,
              logo_1: logo_1,
              logo_2: logo_2,
              id_e_cliente: {rut: response.data.rut}
            }) 
            .then((response) => {

            })
            .catch(e => {
            })
          
            axios.post(config.API_LOCATION + '/user/funcionario/', { // POST A Funcionario
              rut: rutFuncionario,
              nombre_1: nombreFuncionario1,
              nombre_2: nombreFuncionario2,
              apellido_p: apellido_p,
              apellido_m: apellido_m,
              email: emailFuncionario,
              fono: fono,
              movil: movil,
              direccion: direccionFuncionario,
              id_e_cliente: {rut: response.data.rut}
             }) 
              .then((responseFuncionario) => {
                 axios.post(config.API_LOCATION + '/user/usuario/', { // POST a configuracion Cliente 
                    id_funcionario: {rut: responseFuncionario.data.rut},
                    password: password,
                    estado: true,
                  }) 
                  .then((response) => {

                  })
                  .catch(e => {
                  })
              })
              .catch(e => {
              })
          })
          .catch(e => {
          })
          
			},
			editarFuncion (e) {
        axios.put(config.API_LOCATION + '/user/funcion/'+ this.editedItem.id +'', this.editedItem) 
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
        axios.put(config.API_LOCATION + '/user/funcion/'+ this.mantencionItem.id +'', this.mantencionItem)
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
      prueba () {
        console.log(this.serviciosCarro)
        for (var i = 0; i < this.serviciosCarro.length; i++) {
            console.log('entre')
        }
      },
      agregarCarro (item) {
        console.log(item)
        this.serviciosCarro.push(item)
        var indexArray = this.servicios.findIndex(x => x.id === item.id)
        console.log(indexArray)
        this.servicios.splice(indexArray, 1)
        this.totalServicio += item.id_servicio.precio
      },
      quitarCarro (item) {
        console.log(item)
        this.servicios.push(item)
        var indexArray = this.serviciosCarro.findIndex(x => x.id === item.id)
        console.log(indexArray)
        this.serviciosCarro.splice(indexArray, 1)
        this.totalServicio -= item.id_servicio.precio
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
      cerrarModalAgregar () {
        this.dialogAdd = false
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
