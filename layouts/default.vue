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
        
        <v-menu offset-y>
        <v-btn icon slot="activator">
          <v-icon>insert_chart</v-icon>
        </v-btn>
        <v-list dense>
          <v-menu offset-y open-on-hover>
            <v-list-tile slot="activator" @click="">
              <v-list-tile-title>Reportes</v-list-tile-title>
              <v-list-tile-action class="justify-center">
                <v-icon>play_arrow</v-icon>
              </v-list-tile-action>
            </v-list-tile>
            <v-list dense>
              <v-list-tile to="/reporteMayorSolicitante">
                <v-list-tile-title>Mayor Solicitante</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteHerramientaCritico">
                <v-list-tile-title>Herramienta Stock Crítico</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteInsumoCritico">
                <v-list-tile-title>Insumo Stock Crítico</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteInsumoSinStock">
                <v-list-tile-title>Insumo Sin Stock</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteHerramientaSinStock">
                <v-list-tile-title>Herramienta Sin Stock</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteInventarioHerramienta">
                <v-list-tile-title>Inventario Herramienta</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteInventarioInsumo">
                <v-list-tile-title>Inventario Insumo</v-list-tile-title>
              </v-list-tile>
              <v-list-tile to="/reporteHerramientaPrestada">
                <v-list-tile-title>Herramientas no devueltas</v-list-tile-title>
              </v-list-tile>
            </v-list>
          </v-menu>
        </v-list>
      </v-menu>
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
            title: 'Gestión Bodega',
            active: false,
            items: [
              { title: 'Salida de Insumos', to: '/salidaInsumos' },
              { title: 'Registrar Categoria', to: '/categoriaInsumo' },
              { title: 'Registrar Insumo', to: '/insumos' },
              { title: 'Registrar Subcategoria', to: '/subcategoriaInsumo' }
            ]
          },
          {
            action: 'content_cut',
            title: 'Gestión Pañol',
            active: false,
            items: [
              { title: 'Ingresar Herramienta', to: '/herramientas' },
              { title: 'Registrar Préstamo', to: '/prestamo' },
              { title: 'Historial Devolución', to: '/devolucion' },
              { title: 'Registrar Categoria', to: '/categoriaHerramienta' },
              { title: 'Registrar Subcategoria', to: '/subcategoriaHerramienta' }
            ]
          },
          {
            action: 'account_circle',
            title: 'Gestión Administrativa',
            active: false,
            items: [
              { title: 'Tipo', to: '/tipo' },
              { title: 'Crear Orden de Compra', to: '/ordenCompra' },
              { title: 'Ingresar Cotización', to: '/cotizacion' },
              { title: 'Registrar Compra', to: '/compra' },
              { title: 'Registros de Recepcion de Compra', to: '/recepcion' },
              { title: 'Ingresar Tipo Unidad', to: '/tipoUnidad' },
              { title: 'Ingresar Unidad Medida', to: '/unidad' },
              { title: 'Ingresar Marca', to: '/marca' },
              { title: 'Proveedor Nacional', to: '/proveedorN' },
              { title: 'Proveedor Extranjero', to: '/proveedorE' },
              { title: 'Registrar Merma', to: '/merma' }
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
