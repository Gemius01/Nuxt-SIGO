<template>
  <v-app inspire>
    <v-navigation-drawer
      fixed
      clipped
      app
      v-model="drawer"
    >
     <v-card-media src="https://image.ibb.co/nwRVYS/logo.png" height="150px">
     </v-card-media>
      <v-subheader>Módulo de usuario</v-subheader>
       <v-list>
          <v-list-group
            v-model="item.active"
            v-for="item in items"
            :key="item.title"
            :prepend-icon="item.action"
            no-action
          >
            <v-list-tile slot="activator">
              <v-list-tile-content>
                <v-list-tile-title>{{ item.title }}</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile :to="subItem.to" v-for="subItem in item.items" :key="subItem.title" @click="prueba" >
              <v-list-tile-content>
                <v-list-tile-title>{{ subItem.title }}</v-list-tile-title>
              </v-list-tile-content>
              <v-list-tile-action>
                <v-icon>{{ subItem.action }}</v-icon>
              </v-list-tile-action>
            </v-list-tile>
          </v-list-group>
        </v-list>
    </v-navigation-drawer>
    <v-toolbar
      color="blue darken-3"
      dark
      app
      clipped-left
      fixed
    >
    <v-toolbar-title :style="$vuetify.breakpoint.smAndUp ? 'width: 300px; min-width: 250px' : 'min-width: 72px'" class="ml-0 pl-3">
        <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
        <span class="hidden-xs-only" v-text="title"></span>
      </v-toolbar-title>
      <v-text-field
        light
        solo
        prepend-icon="search"
        placeholder="Buscar"
        style="max-width: 500px; min-width: 128px"
      ></v-text-field>
      <div class="d-flex align-center" style="margin-left: auto">
        <v-btn icon>
          <v-icon>notifications</v-icon>
        </v-btn>
      </div>
    </v-toolbar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-navigation-drawer
      temporary
      :right="right"
      v-model="rightDrawer"
      fixed
    >
      <v-list>
        <v-list-tile @click.native="right = !right">
          <v-list-tile-action>
            <v-icon light>compare_arrows</v-icon>
          </v-list-tile-action>
          <v-list-tile-title>Switch drawer (click me)</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-footer :fixed="fixed" app>
      <span>&copy; 2017</span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data () {
      return {
        clipped: false,
        drawer: false,
        fixed: false,
        items: [
          {
            action: 'assignment',
            title: 'Módulos',
            active: false,
            items: [
              { title: 'Módulo', to: '/usuario/modulo' }
            ]
          },
          {
            action: 'content_cut',
            title: 'Funciones',
            active: false,
            items: [
              { title: 'Función', to: '/usuario/funcion' }
            ]
          },
          {
            action: 'account_circle',
            title: 'Servicios',
            active: false,
            items: [
              // { title: 'Tipo', to: '/tipo' },
              { title: 'Crear Servicio', to: '/usuario/servicios' }
            ]
          },
          {
            action: 'insert_chart',
            title: 'Contratos',
            active: false,
            items: [
              { title: 'Crear Contrato', to: '/usuario/contrato' }
            ]
          },
          {
            action: 'insert_chart',
            title: 'Usuarios',
            active: false,
            items: [
              { title: 'Crear Usuario', to: '/usuario' }
            ]
          },
          {
            action: 'insert_chart',
            title: 'Perfiles',
            active: false,
            items: [
              { title: 'Mayor Solicitante', to: '/reporteMayorSolicitante' }
            ]
          }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'ERP ITempus'
      }
    },
    methods: {
      prueba () {
      }
    }
  }
</script>
