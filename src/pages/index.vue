<template>
  <v-app>

    <!-- Barra de navegación -->
    <v-app-bar app color="black" dark>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Images</v-toolbar-title>
    </v-app-bar>

    <!-- Menú lateral -->
    <v-navigation-drawer v-model="drawer" app>
      <v-list dense>
        <v-list-item-group>
          <v-list-item @click="$router.push('/home')">
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item>
          <v-list-item @click="$router.push('/peliculas')">
            <v-list-item-title>Titulos</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <!-- Contenido de las rutas -->
    <v-card class="mx-auto custom-card" max-width="400" style="position: relative;">
      <!-- Overlay dentro de la tarjeta -->
      <v-overlay :value="overlay" absolute>
        <v-progress-circular color="primary" size="64" indeterminate></v-progress-circular>
      </v-overlay>

      <v-img :src="currentImage" class="align-end text-white" height="200" cover>
        <v-card-title>Top 10 Australian beaches</v-card-title>
      </v-img>

      <v-card-subtitle class="pt-4">Number 10</v-card-subtitle>

      <v-card-text>
        <div>Whitehaven Beach</div>
        <div>Whitsunday Island, Whitsunday Islands</div>
      </v-card-text>

      <v-card-actions>
        <v-btn color="orange" @click="obtenerImagenes">Cambiar</v-btn>
      </v-card-actions>
    </v-card>

    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      overlay: false,
      show: false,
      drawer: false, // Controla si el menú lateral está abierto o cerrado
      currentImage: "https://cdn.vuetifyjs.com/images/cards/docks.jpg", // Imagen inicial
    };
  },
  methods: {
    async obtenerImagenes() {
      try {
        this.overlay = true; // Activar el overlay cuando se inicia la carga
        this.currentImage = `https://picsum.photos/400/200?random=${Math.random()}`;
      } catch (error) {
        console.error("Error al obtener las imágenes:", error);
      } finally {
        this.overlay = false; // Desactivar el overlay cuando se completa la carga
      }
    },
  },
};
</script>

<style scoped>
#app {
  min-height: 100vh;
}

/* Añade espacio por encima de la tarjeta */
.custom-card {
  margin-top: 50px; /* Ajusta este valor para mover la tarjeta más arriba o abajo */
  position: relative;
}
</style>
