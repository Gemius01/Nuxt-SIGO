<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm12 md3>
            <v-card class="elevation-12">
              <H1>LOGIN</H1>
              <v-card-text>
                <v-form>
                  <v-text-field prepend-icon="person" name="login" label="Usuario" type="text"></v-text-field>
                  <v-text-field prepend-icon="lock" name="password" label="Contraseña" id="password" type="password"></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-flex xs12 sm12 md12>
                <v-spacer></v-spacer>
                <v-btn block color="secondary" dark>Login</v-btn>
              </v-flex>
              </v-card-actions>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      drawer: null
    }),
    props: {
      source: String
    }
  }
</script>
<template>
  <v-app id="inspire">
    <v-card-media src="https://image.freepik.com/foto-gratis/escritorio-de-madera-con-borrosa-de-sala-de-oficina-interior-con-fondo-de-construccion-de-la-ciudad_3249-1616.jpg" height="730">
    <v-content>
      <v-container fluid fill-height>
          <v-layout align-center justify-center>
          <v-flex xs12 sm3>
            <v-card class="elevation-12">
              <v-toolbar color="blue" dark>
                <v-toolbar-side-icon></v-toolbar-side-icon>
                  <v-toolbar-title>L O G I N</v-toolbar-title>
                    <v-spacer></v-spacer>
            </v-toolbar>
            <v-form @submit.prevent="logearse">
              <v-card-text>
                
                  <v-text-field prepend-icon="person" id="user" name="login" label="Usuario" type="text"></v-text-field>
                  <v-text-field prepend-icon="lock" id="password" name="password" label="Contraseña" type="password"></v-text-field>
                
              </v-card-text>
              <v-card-actions>
                <v-flex xs12 sm12 md12>
                <v-spacer></v-spacer>
                <v-btn block color="blue" dark type="submit">Login</v-btn>
              </v-flex>
              </v-card-actions>
              </v-form>
            </v-card
          </v-flex>
          </v-flex>
          </v-layout>
      </v-container>
    </v-content>
    </v-card-media>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      drawer: null
    }),
    props: {
      source: String
    },
    methods: {
      logearse () {
        var user = document.getElementById('user').value
        var password = document.getElementById('password').value
        if (user === 'admin' && password === 'admin') {
          window.location.replace('http://localhost:3000/')
        } else {
          alert('Clave o Usuario Incorrecto')
        }
      }
    }
  }
</script>

