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
      <v-subheader>Administración Bodega y Pañol</v-subheader>
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
        <v-menu offset-y>
        <v-btn  icon slot="activator"> <v-icon>account_circle</v-icon></v-btn>
        <v-list>
          <v-list-tile to="/userProfile" >
            <v-list-tile-title >Perfil</v-list-tile-title>
          </v-list-tile>
          <v-list-tile @click="logOut">
            <v-list-tile-title >Cerrar Sesión</v-list-tile-title>
          </v-list-tile>
        </v-list>
      </v-menu>
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
  import Cookie from 'js-cookie'
  import axios from 'axios'
  import config from '../config.vue'
  export default {
    data () {
      return {
        clipped: false,
        drawer: false,
        fixed: false,
        items: [
          {
            action: '',
            title: '',
            active: false,
            items: []
          }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'ERP ITempus'
      }
    },
    created () {
      this.initialize()
    },
    methods: {
      initialize () { // Función que recarga los datos de la Tabla mediante request a la API REST
        axios.get(config.API_LOCATION + '/user/menu/') // petición GET a Tipo para traer a todos los objetos "tipo"
          .then((response) => {
          this.items = [response.data]
          })
          .catch(e => {
          })
      },
      prueba () {
      },
      logOut() {
        Cookie.remove('auth')
        Cookie.remove('rol')
        //var TOKEN_KEY = "jwtToken"
        //localStorage.removeItem(TOKEN_KEY)
        window.location.replace('http://localhost:3000/login')
      }
    }
  }
</script>
