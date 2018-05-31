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
                            <v-flex xs6>
                              <v-select item-value="id" item-text="nombre" :items="ciudades" v-model="selectCiudad" label="Ciudad" single-line
                              ></v-select>
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
                              <v-select item-value="id" item-text="nombre" :items="ciudades" v-model="selectCiudadFuncionario" label="Ciudad" single-line
                              ></v-select>
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
                              <td class="text-xs-center" @click="prueba">{{ props.item.nombre }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.precio }}</td>
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
                              <td class="text-xs-center" @click="prueba">{{ props.item.nombre }}</td>
                              <td class="text-xs-center" @click="prueba">{{ props.item.precio }}</td>
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
                          <v-layout row wrap>
                          <v-spacer></v-spacer>
                          <v-flex xs3>
                          <v-text-field
                            id="diaPago"
                            type="number"
                            label="Día Pago"
                          ></v-text-field>
                        </v-flex>
                          <v-flex xs12 sm6 md4>
                            <v-dialog
                              ref="dialogInicio"
                              v-model="modalInicio"
                              :return-value.sync="dateInicio"
                              persistent
                              lazy
                              full-width
                              width="290px"
                            >
                              <v-text-field
                                slot="activator"
                                v-model="dateInicio"
                                label="Picker in dialog"
                                prepend-icon="event"
                                readonly
                              ></v-text-field>
                              <v-date-picker v-model="dateInicio" scrollable>
                                <v-spacer></v-spacer>
                                <v-btn flat color="primary" @click="modalInicio = false">Cancel</v-btn>
                                <v-btn flat color="primary" @click="$refs.dialogInicio.save(dateInicio)">OK</v-btn>
                              </v-date-picker>
                            </v-dialog>
                          </v-flex>
                          <v-spacer></v-spacer>
                                <v-spacer></v-spacer>
                          <v-flex xs12 sm6 md4>
                            <v-dialog
                              ref="dialogTermino"
                              v-model="modalTermino"
                              :return-value.sync="dateTermino"
                              persistent
                              lazy
                              full-width
                              width="290px"
                            >
                              <v-text-field
                                slot="activator"
                                v-model="dateTermino"
                                label="Picker in dialog"
                                prepend-icon="event"
                                readonly
                              ></v-text-field>
                              <v-date-picker v-model="dateTermino" scrollable>
                                <v-spacer></v-spacer>
                                <v-btn flat color="primary" @click="modalTermino = false">Cancel</v-btn>
                                <v-btn flat color="primary" @click="$refs.dialogTermino.save(dateTermino)">OK</v-btn>
                              </v-date-picker>
                            </v-dialog>
                          </v-flex>
                          <v-spacer></v-spacer>
                                <v-spacer></v-spacer>
                          <v-flex xs12 sm6 md4>
                            <v-dialog
                              ref="dialogPago"
                              v-model="modalPago"
                              :return-value.sync="datePago"
                              persistent
                              lazy
                              full-width
                              width="290px"
                            >
                              <v-text-field
                                slot="activator"
                                v-model="datePago"
                                label="Picker in dialog"
                                prepend-icon="event"
                                readonly
                              ></v-text-field>
                              <v-date-picker v-model="datePago" scrollable>
                                <v-spacer></v-spacer>
                                <v-btn flat color="primary" @click="modalPago = false">Cancel</v-btn>
                                <v-btn flat color="primary" @click="$refs.dialogPago.save(datePago)">OK</v-btn>
                              </v-date-picker>
                            </v-dialog>
                          </v-flex>
                          <v-spacer></v-spacer>
                        </v-layout>
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
     <!-- Dialog Detalle Contrato -->
     <v-layout row justify-center>
      <v-dialog v-model="dialogdetailContrato" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-btn slot="activator" color="primary" dark>Open Dialog</v-btn>
        <v-card>
          <v-toolbar dark color="primary">
            <v-btn icon dark @click.native="dialogdetailContrato = false">
              <v-icon>close</v-icon>
            </v-btn>
            <v-toolbar-title>Settings</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn dark flat @click.native="dialogdetailContrato = false">Save</v-btn>
            </v-toolbar-items>
          </v-toolbar>
          <v-tabs icons-and-text centered dark color="cyan">
      <v-tabs-slider color="yellow"></v-tabs-slider>
      <v-tab href="#tab-1">
        Datos Empresa
        <v-icon>description</v-icon>
      </v-tab>
      <v-tab href="#tab-2">
        Usuarios
        <v-icon>supervised_user_circle</v-icon>
      </v-tab>
      <v-tab href="#tab-3">
        Pagos
        <v-icon>credit_card</v-icon>
      </v-tab>
      <v-tab href="#tab-4">
        Documentos
        <v-icon>code</v-icon>
      </v-tab>
      <v-tab href="#tab-5">
        Servicios
        <v-icon>store</v-icon>
      </v-tab>
      <v-tab-item
        :id="'tab-1'"
      >
        <v-card flat>
          <h1>{{ detailEmpresa.id_e_cliente.nombre}}</h1>
          <v-divider></v-divider>
           <v-flex xs6>
              <v-list dense >
                <v-list-tile class="hoverMouse">
                <v-list-tile-title>RUT</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailEmpresa.id_e_cliente.rut }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Dirección</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailEmpresa.id_e_cliente.direccion }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Fono</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailEmpresa.id_e_cliente.fono }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>E-Mail</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailEmpresa.id_e_cliente.email }}</v-list-tile-title>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Contacto</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <v-list-tile-title>{{ detailEmpresa.id_e_cliente.contacto }}</v-list-tile-title>
              </v-list-tile>
            </v-list>
          </v-flex>
          <v-divider></v-divider>
          <h1>Configuración Empresa</h1>
          <v-flex xs6>
          <v-list dense >
                <v-list-tile class="hoverMouse">
                <v-list-tile-title>Color Primario</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <div :style="detailConfEmpresa.color_p" class="colorPrimario"></div>
              </v-list-tile>
              <v-list-tile class="hoverMouse">
                <v-list-tile-title>Color Secundario</v-list-tile-title>
                <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
                <div :style="detailConfEmpresa.color_s" class="colorPrimario"></div>
              </v-list-tile>
            </v-list>
          </v-flex>
        </v-card>
      </v-tab-item>
      <v-tab-item
        :id="'tab-2'"
      >
        <v-card flat>
          <v-card-text>lorem ipsum2</v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item
        :id="'tab-3'"
      >
        <v-card flat>
          <v-card-text>lorem ipsum2</v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item
        :id="'tab-4'"
      >
        <v-card flat>
          <v-card-text>lorem ipsum2</v-card-text>
        </v-card>
      </v-tab-item>
      <v-tab-item
        :id="'tab-5'"
      >
        <v-card flat>
          <v-card-text>lorem ipsum5</v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs>
        </v-card>
      </v-dialog>
    </v-layout>
    <!-- Fin Dialog Detalle Contrato -->

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
      :items="contratos"
      :search="search"
      must-sort
      :pagination.sync="pagination"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.id_e_cliente.nombre }}</td>
        <td class="text-xs-center">{{ props.item.valor_memb }}</td>
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
          <!--
          <v-tooltip top>
          <v-btn icon slot="activator" class="mx-0" @click="modalMantencion(props.item)" >
            <v-icon color="orange darken-1">build</v-icon>
          </v-btn>
          <span>Mantención</span>
          </v-tooltip>
        -->
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
            { text: 'Valor Membresia', value: 'valor_memb', width: '25%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
		    ],
        headersTablaServicios: [
            { text: 'ID', value: 'rut', sortable: true, width: '25%', align: 'center' },
            { text: 'Nombre', value: 'nombre', width: '25%', align: 'center' },
            { text: 'Precio', value: 'precio', width: '25%', align: 'center' },
            { text: 'Opciones', sortable: false, width: '25%', align: 'center' }
        ],
      contratos: [],
			modulos: [],
      ciudades: [],
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
      selectCiudad: 0,
      selectCiudadFuncionario: 0,
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
      dialogdetailContrato: false,
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
      // Date Fecha Inicio
      dateInicio: null,
      menuInicio: false,
      modalInicio: false,
      menu2Inicio: false,
      // Date Fecha Termino
      dateTermino: null,
      menuTermino: false,
      modalTermino: false,
      menu2Termino: false,
      // Date Fecha Pago
      datePago: null,
      menuPago: false,
      modalPago: false,
      menu2Pago: false,

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
        },
        detailEmpresa: {
            id: 0,
            id_e_cliente: {
              rut: '',
              nombre: '',
              direccion: '',
              fono: '',
              email: '',
              contacto: '',
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
              }
            },
            valor_memb: 0,
            fecha: '',
            estado: true,
            dia_pago: 0,
            fecha_termino: ''
          },
          detailConfEmpresa: {
            id: 0,
            color_p: '',
            color_s: '',
            logo_1: '',
            logo_2: '',
            id_e_cliente: {
              rut: '',
              nombre: '',
              direccion: '',
              fono: '',
              email: '',
              contacto: '',
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
              }
            }
          }
		}),
    created () {
      this.initialize()
      this.cargarModulos()
      this.cargarServicios()
      this.cargarCiudades()
    },
		methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/contrato/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.contratos = response.data
          })
          .catch(e => {
          })
      },
      cargarCiudades () {
        axios.get(config.API_LOCATION + '/bodega/ciudad/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
            this.ciudades = response.data
          })
          .catch(e => {
          })
      },
      changePrueba (e) {
        console.log(e.target.value)
      },
      pruebas () {
        console.log(this.detailConfEmpresa)
        /*
        console.log(document.getElementById('fonoEmpresa').value)
        console.log(this.colorPrimario)
        console.log(this.colorSecundario)
        console.log(this.imagen)
        console.log(this.ciudad)
        */
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
        axios.get(config.API_LOCATION + '/user/servicio/') // petición GET a Tipo para traer a todos los objetos "tipo"
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
        var fonoEmpresa = document.getElementById('fonoEmpresa').value
        var direccion = document.getElementById('direccionEmpresa').value
        var contacto = document.getElementById('nombreContactoEmpresa').value
        var email = document.getElementById('emailContactoEmpresa').value
        var id_ciudad = this.selectCiudad
        

        // Funcionario
        var rutFuncionario = document.getElementById('rutAdministrador').value
        var nombreFuncionario1 = document.getElementById('primerNombre').value
        var nombreFuncionario2 = document.getElementById('segundoNombre').value
        var apellido_p = document.getElementById('primerApellido').value
        var apellido_m = document.getElementById('segundoApellido').value
        var direccionFuncionario = document.getElementById('direccionFuncionario').value
        var ciudad = this.selectCiudadFuncionario
        var fonoFuncionario = document.getElementById('fonoFuncionario').value
        var movil = document.getElementById('movilFuncionario').value
        var emailFuncionario = document.getElementById('emailFuncionario').value
        var password = document.getElementById('confirmarContraseña').value
        // var e-cliente = 

        // conf cliente
        var color_p = this.colorPrimario.hex
        var color_s = this.colorSecundario.hex
        var logo_1 =  ''
        var logo_2 =  ''

        //Contrato
        var diaPago = document.getElementById('diaPago').value

       
        axios.post(config.API_LOCATION + '/user/e_cliente/', { //POST a Empresa Cliente
          rut: rut,
          nombre: nombre,
          fono: fonoEmpresa,
          direccion: direccion,
          contacto: contacto,
          email: email,
          id_ciudad: {id: id_ciudad}
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
              dia_pago: diaPago
             }) 
             .then((responseContrato) => {
              console.log(responseContrato.data)
              var idContrato = responseContrato.data.id
              console.log(idContrato)
              /*
              axios.post(config.API_LOCATION + '/user/pago/', { // POST a anexo 
                  id_contrato: {id: responseContrato.data.id},
                  mes
                  id_servicio: {id: this.serviciosCarro[i].id_servicio.id},
                  fecha_inicio: '',
                  fecha_termino: '',
                  fecha_pago: ''
                 }) 
                 .then((responsePago) => {

                 }).catch(e => {

                 })
              */
              for (var i = 0; i < this.serviciosCarro.length; i ++) {
                var id_servicioCarro = this.serviciosCarro[i].id
                console.log('id_servicio')
                console.log(id_servicioCarro)
                console.log('id_contrato')
                console.log(idContrato)
                axios.post(config.API_LOCATION + '/user/anexo/', { // POST a anexo 
                  id_contrato: {id: idContrato},
                  id_servicio: {id: id_servicioCarro},
                  fecha_inicio: ''
                 }) 
                 .then((responseAnexo) => {
                  
                  var id_Anexo = responseAnexo.data.id
                  console.log(id_servicioCarro)
                  console.log(id_Anexo)
                  axios.get(config.API_LOCATION + '/user/d_servicio/' + id_servicioCarro + '/servicio')
                  .then((responseDServicio) => {

                    for (var e = 0; e < responseDServicio.data.length; e++) {
                      ////////////////
                      console.log('entre al for d_servicio')
                      console.log('' + responseDServicio.data[e].limit_reg + ' === null')
                      if (responseDServicio.data[e].limit_reg != null) {
                      axios.post(config.API_LOCATION + '/user/cupo/', { // POST a Cupo 
                        id_anexo: {id: id_Anexo},
                        id_d_servicio: {id: responseDServicio.data[e].id},
                        lleno: false
                      })
                      .then((response) => {

                      })
                      .catch(e => {
                      })
                      /////////
                      }
                    }
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
              fono: fonoFuncionario,
              movil: movil,
              direccion: direccionFuncionario,
              id_e_cliente: {rut: response.data.rut},
              id_ciudad: {id: this.selectCiudadFuncionario}
             }) 
              .then((responseFuncionario) => {
                 axios.post(config.API_LOCATION + '/user/usuario/', { // POST a usuario 
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
        this.totalServicio += item.precio
      },
      quitarCarro (item) {
        console.log(item)
        this.servicios.push(item)
        var indexArray = this.serviciosCarro.findIndex(x => x.id === item.id)
        console.log(indexArray)
        this.serviciosCarro.splice(indexArray, 1)
        this.totalServicio -= item.precio
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
				this.detailEmpresa = item
        
        axios.get(config.API_LOCATION + '/user/conf_e_cliente/' + this.detailEmpresa.id_e_cliente.rut + '/e_cliente')
          .then((response) => {
           this.detailConfEmpresa = response.data[0]
           this.detailConfEmpresa.color_p = 'background-color:' + this.detailConfEmpresa.color_p + ';'
           this.detailConfEmpresa.color_s = 'background-color:' + this.detailConfEmpresa.color_s + ';'
          })
          .catch(e => {
          })
         
				this.dialogdetailContrato = true
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
<style>
  .colorPrimario {
    border-style: solid;
    border-width: 2px;
    border-color: black;
    border-radius: 4px;
    width:50%;
    height: 50%;
  }
</style>
