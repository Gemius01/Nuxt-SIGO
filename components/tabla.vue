<template>
  <div>
     <v-dialog v-model="dialog" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">{{ formTitle }}</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Dessert name" v-model="editedItem.name"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Calories" v-model="editedItem.calories"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Fat (g)" v-model="editedItem.fat"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Carbs (g)" v-model="editedItem.carbs"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Protein (g)" v-model="editedItem.protein"></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
          <v-btn color="blue darken-1" flat @click.native="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-card>
      <v-card-title>
        <slot name="titulo">
        </slot>
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
    >
      <template slot="items" slot-scope="props">
        <tr ref="props.item" @click.stop="addEditDialogOpen(props.item.id)">
          <td :key="header.value" v-for="header in headers">{{ props.item[header.value] | formatters(header.value) }}</td>
          <td class="justify-center layout px-0">
           
             <v-tooltip  top>
            <v-btn icon icon slot="activator" class="mx-0" @click="detailItem()">
              <v-icon color="blue">search</v-icon>
            </v-btn>
            <span>Ver Detalle</span>
            </v-tooltip>

            <v-tooltip  top>
            <v-btn icon slot="activator" class="mx-0" @click="editItem(props.item)">
              <v-icon color="teal">edit</v-icon>
            </v-btn>
            <span>Editar</span>
            </v-tooltip>
            <v-tooltip  top>
            <v-btn icon slot="activator" icon class="mx-0" @click="deleteItem(props.item)">
              <v-icon color="pink">delete</v-icon>
            </v-btn>
            <span>Eliminar</span>
            </v-tooltip>
            
          </td>
        </tr>
      </template>
      <template slot="pageText" slot-scope="{ pageStart, pageStop }">
        De {{ pageStart }} a {{ pageStop }}
      </template>
    </v-data-table>
    <v-card-text>
        <v-tooltip top>
          <v-btn
            dark color="pink"
            slot="activator"
            fab
            absolute
            @click="newItem()"
            >
            <v-icon>add</v-icon>
          </v-btn>
          <span>Agregar</span>
        </v-tooltip>

    </v-card-text>
  
      <v-dialog v-model="dialog2" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Detalle</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Dessert name" disabled></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Calories"  disabled></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Fat (g)"  disabled></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Carbs (g)"  disabled></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field label="Protein (g)"  disabled></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
          </v-card-actions>
      </v-card>
      
    </v-dialog>
    </v-card>

         <v-dialog v-model="dialog3" persistent max-width="350">
      
      <v-card>
        <v-card-title class="headline">¿Está seguro de eliminar?</v-card-title>
        
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" flat @click.native="dialog3 = false">Cancelar</v-btn>
          <v-btn color="blue" flat @click="confirmdelete()">Eliminar</v-btn>
          
        </v-card-actions>
      </v-card>
    </v-dialog>
    </v-card>

  </div>
</template>

<script>
  export default {
    props: [
      'crudTable', 'mitems'
    ],
    data: () => ({
      dialog: false,
      dialog2: false,
      dialog3: false,
      search: '',
      pagination: {},
      headers: { }, // pass in
      items: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        calories: 0,
        fat: 0,
        carbs: 0,
        protein: 0
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
      this.headers = this.crudTable.headers
      this.items = this.mitems
    },
    methods: {
      newItem () {
        this.editedIndex = -1
        this.dialog = true
      },
      editItem (item) {
        this.editedIndex = this.items.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },
      detailItem () {
        this.editedIndex = -1
        this.dialog2 = true
      },
      deleteItem () {
        this.dialog3 = true
      },
      confirmdelete (item) {
        const index = this.items.indexOf(item)
        this.items.splice(index, 1)
        this.dialog3 = false
      },
      close () {
        this.dialog = false
        this.dialog2 = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },
      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          this.items.push(this.editedItem)
        }
        this.close()
      }
    }
  }
</script>
