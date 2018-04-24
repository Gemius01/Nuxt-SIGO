<template>
	<div>
		 <!-- Dialog Agregar Bodega -->
    <v-dialog v-model="dialogAdd" max-width="500px" >
      <v-btn dark color="primary"  slot="activator" >Agregar Bodega</v-btn>
            <v-form @submit.prevent="agregarBodega" v-model="valid" ref="fAgregarHerramientas" lazy-validation>
      <v-card>
        <v-card-title>
          <span class="headline">Nueva Bodega</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	
            	<v-flex xs12>
            		<v-text-field label="Empresa Cliente" :counter="20"></v-text-field>
            	</v-flex>
            	<v-flex xs12>
            		<v-text-field label="Dirección" :counter="20"></v-text-field>
            	</v-flex>
            	<v-flex xs12>
            		<v-select label="Ciudad" ></v-select>
            	</v-flex>
              <v-flex xs12>
                <v-text-field label="Fono" :counter="20"></v-text-field>
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
    <!-- Fin Dialog Agregar Bodega -->
    <!-- Dialog Editar Bodega -->
       <v-dialog v-model="dialogEdit" max-width="500px">
        <v-form @submit.prevent="editarBodega" ref="fEditarHerramientas">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Bodega</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
            	<v-flex xs12>
            		<v-text-field label="Empresa Cliente" v-model="editedItem.e_cliente" :counter="20"></v-text-field>
            	</v-flex>
            	<v-flex xs12>
            		<v-text-field label="Fono" v-model="editedItem.fono" :counter="20"></v-text-field>
            	</v-flex>
              <v-flex xs12>
                <v-text-field label="Dirección" v-model="editedItem.direccion" :counter="20"></v-text-field>
              </v-flex>
            	<v-flex xs12>
            		<v-select label="Ciudad" v-model="editedItem.ciudad.nombre" :counter="20"></v-select>
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
    <!-- Fin Dialog Editar Bodega -->
     <!-- Dialog Detalle Bodega -->
        <v-dialog v-model="dialogDetail" max-width="500px">
        <form @submit.prevent="">
      <v-card>

          <v-card-title><h1> Detalle de Bodega</h1></v-card-title>
          <v-divider></v-divider>
          <v-list dense >
              <v-list-tile class="hoverMouse">
              <v-list-tile-title>RUT</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.id }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Nombre</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.e_cliente }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Fono</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.fono }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>Dirección</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.direccion }}</v-list-tile-title>
            </v-list-tile>
            <v-list-tile class="hoverMouse">
              <v-list-tile-title>E-Mail</v-list-tile-title>
              <v-list-tile-title class="text-lg-center">:</v-list-tile-title>
              <v-list-tile-title>{{ detailItem.ciudad.nombre }}</v-list-tile-title>
            </v-list-tile>
           
          </v-list>
       
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="cerrarModalDetail">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
       </form>
     </v-dialog>
    <!-- Fin Dialog Editar Bodega -->
	<!-- Tabla -->
    <v-card>
    <v-card-title>
        <h1>Bodegas</h1>
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
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.e_cliente }}</td>
        <td class="text-xs-center" >{{ props.item.fono }}</td>
        <td class="text-xs-center" >{{ props.item.ciudad.nombre }}</td>
        <td class="text-xs-center" style="display:none;">{{ props.item.direccion }}</td>       
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
<!-- Fin Tabla-->

</div>
</template>
<script>
	export default {
		data: () => ({
			headers: [
		        { text: 'ID', value: 'id', sortable: true, width: '20%', align: 'center' },
		        { text: 'Empresa Cliente', value: 'e_cliente', width: '20%', align: 'center' },
		        { text: 'Fono', value: 'fono', width: '20%', align: 'center' },
            { text: 'Ciudad', value: 'ciudad', width: '20%', align: 'center' },
		        { text: 'Opciones', sortable: false, width: '20%', align: 'center' }
		    ],
			items: [{id: 100, e_cliente: 'Coca Cola', fono: 100, ciudad: {id: 1, nombre: 'Iquique'}, direccion: 'Corral #123'}],
			pagination: {},
			search: '',
			valid: true,
			dialogAdd: false,
			dialogEdit: false,
			dialogDetail: false,
			editedItem: { // prop temporal que guarda el objeto a editar o eliminar
	        rut: 0,
          e_cliente: '',
          fono: '',
          ciudad: {id: 0, nombre: ''},
          direccion: ''
		    },
		    detailItem: { // prop temporal que guarda el objeto a mostrar en detalle
		      rut: 0,
		      e_cliente: '',
		      fono: '',
		      ciudad: {id: 0, nombre: ''},
          direccion: ''
		    }
		}),
		methods: {
			agregarBodega () {

			},
			editarBodega () {
				alert('Bodega editada')
			},
			modalEdit (item) {
				this.editedItem = item
				this.dialogEdit = true
			},
			modalDetalle (item) {
				this.detailItem = item
				this.dialogDetail = true
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