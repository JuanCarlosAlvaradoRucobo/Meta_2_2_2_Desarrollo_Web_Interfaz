<template>
  <div>
    <v-app-bar app color="black" dark>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-app-bar-title>Home</v-app-bar-title>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" app>
      <v-list dense>
        <v-list-item @click="$router.push('/')">
          <v-list-item-title>Inicio</v-list-item-title>
        </v-list-item>
        <v-list-item @click="$router.push('/peliculas')">
          <v-list-item-title>Películas</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-container class="fill-height">
      <v-responsive
        class="align-center fill-height mx-auto"
        max-width="900"
      >
        <v-img
          class="mb-4"
          height="150"
          src="@/assets/logo.png"
        />

        <div class="text-center">
          <div class="text-body-2 font-weight-light mb-n1">Welcome to</div>
          <h1 class="text-h2 font-weight-bold">Vuetify</h1>
        </div>

        <div class="py-4" />

        <v-row>
          <!-- Contenido de las tarjetas aquí -->
        </v-row>
      </v-responsive>
    </v-container>
  </div>
</template>

<script>
  export default {
    data: () => ({
      drawer: false,
    }),
  }
</script>
