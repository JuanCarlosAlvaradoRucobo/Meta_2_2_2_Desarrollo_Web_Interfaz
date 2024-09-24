<template>
  <div>
    <!-- Barra de navegación -->
    <v-app-bar app color="black" dark>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Titulos</v-toolbar-title>
    </v-app-bar>

    <!-- Menú de navegación lateral -->
    <v-navigation-drawer title="Menu" subtitle="vuetify" v-model="drawer" app>
      <v-list dense>
        <v-list-item @click="$router.push('/')">
          <v-list-item-title>Inicio</v-list-item-title>
        </v-list-item>
        <v-list-item @click="$router.push('/home')">
          <v-list-item-title>Home</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-overlay :model-value="overlay" class="align-center justify-center">
      <v-progress-circular color="primary" size="64" indeterminate></v-progress-circular>
    </v-overlay>

    <Title>Lista de Peliculas</Title>

    <!-- Contenido principal de la página de películas -->
    <v-main>
      <v-container class="fill-height">
        <v-responsive class="align-center fill-height mx-auto" max-width="900">
          <h1>{{ selectedTitle || "" }}</h1>
          <table>
            <thead>
              <tr>
                <th>Título</th>
                <th>Calificación</th>
                <th>IMDB</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="pelicula in peliculas" :key="pelicula.id">
                <td>
                  <button @click="cambiarTitulo(pelicula.movie)">
                    {{ pelicula.movie }}
                  </button>
                </td>
                <td>{{ pelicula.rating }}</td>
                <td>
                  <a :href="pelicula.imdb_url" target="_blank">Ver más</a>
                </td>
                <td class="d-flex justify-center">
                  <!-- Iconos para acciones -->
                  <v-icon @click="editarPelicula(pelicula.id)" class="action-icon">
                    mdi-pencil
                  </v-icon>
                  <v-icon @click="eliminarPelicula(pelicula.id)" class="action-icon" style="margin-left: 10px;">
                    mdi-delete
                  </v-icon>
                </td>
              </tr>
            </tbody>
          </table>
        </v-responsive>
      </v-container>
    </v-main>
  </div>
</template>

<script>
import { mdiPencil, mdiDelete } from '@mdi/js';
import Swal from "sweetalert2";

export default {
  data() {
    return {
      overlay: false,
      drawer: false, // Estado del drawer de navegación
      peliculas: [], // Array para almacenar las películas
      selectedTitle: '', // Título de la película seleccionada
      mdiPencil,
      mdiDelete,
    };
  },
  created() {
    this.obtenerPeliculas(); // Cargar las películas al crear el componente
  },
  methods: {
    async obtenerPeliculas() {
      try {
        this.overlay = true;
        const response = await fetch("https://dummyapi.online/api/movies");
        const data = await response.json();
        this.peliculas = data; // Asignar los datos obtenidos al array de películas
      } catch (error) {
        console.error("Error al obtener las películas:", error);
      } finally {
        this.overlay = false; // Desactivar el overlay cuando se completa la carga
      }
    },
    cambiarTitulo(titulo) {
      this.selectedTitle = titulo; // Cambiar el título seleccionado
    },
    eliminarPelicula(id) {
      const pelicula = this.peliculas.find((p) => p.id === id);
      Swal.fire({
        title: "¿Estás seguro?",
        text: `¿De verdad deseas eliminar "${pelicula.movie}"?`,
        icon: "warning",
        showCancelButton: true,
        confirmButtonText: "Sí, eliminar",
        cancelButtonText: "Cancelar",
      }).then((result) => {
        if (result.isConfirmed) {
          this.deleteMovie(id);
        }
      });
    },
    deleteMovie(id) {
      this.peliculas = this.peliculas.filter((pelicula) => pelicula.id !== id);
      Swal.fire("Eliminado", "La película ha sido eliminada", "success");
    },
    editarPelicula(id) {
      console.log("Editar película con ID:", id);
      const pelicula = this.peliculas.find((p) => p.id === id);

      if (!pelicula) {
        console.error("Película no encontrada");
        return;
      }

      Swal.fire({
        title: "Editar película",
        html: `
          <input id="swal-input1" class="swal2-input" placeholder="Nombre" value="${pelicula.movie}">
          <input id="swal-input2" class="swal2-input" placeholder="IMDB" value="${pelicula.imdb}">
          <input id="swal-input3" class="swal2-input" placeholder="Calificación" type="number" value="${pelicula.rating}">
        `,
        focusConfirm: false,
        showCancelButton: true,
        preConfirm: () => {
          const title = document.getElementById("swal-input1").value;
          const imdb = document.getElementById("swal-input2").value;
          const rating = document.getElementById("swal-input3").value;
          return { title, imdb, rating };
        },
      }).then((result) => {
        if (result.isConfirmed) {
          Swal.fire({
            title: "¿Estás seguro?",
            text: `¿Deseas actualizar la película a "${result.value.title}"?`,
            icon: "warning",
            showCancelButton: true,
            confirmButtonText: "Sí, actualizar",
            cancelButtonText: "Cancelar",
          }).then((confirmResult) => {
            if (confirmResult.isConfirmed) {
              this.updateMovie(id, result.value);
            }
          });
        }
      });
    },
    updateMovie(id, newValues) {
      const pelicula = this.peliculas.find((p) => p.id === id);
      if (pelicula) {
        pelicula.movie = newValues.title;
        pelicula.imdb = newValues.imdb;
        pelicula.rating = newValues.rating;
        Swal.fire("Actualizado", `"${pelicula.movie}" ha sido actualizado`, "success");
      }
    },
  },
  watch: {
    overlay(val) {
      if (val) {
        setTimeout(() => {
          this.overlay = false; // Desactivar el overlay después de 3 segundos
        }, 5000);
      }
    },
  },
};
</script>

<style scoped>
/* Estilos para la tabla */
table {
  width: 100%;
  border-collapse: collapse;
  background-color: #282626;
  border: 2px solid #424243;
  color: #ffffff;
}

th,
td {
  padding: 12px;
  text-align: left;
  border: 1px solid #ffffff;
}

th {
  background-color: #282626;
}

button {
  background-color: transparent;
  border: none;
  color: #ffffff;
  cursor: pointer;
}

button:hover {
  color: #99ccff;
}

button:focus {
  outline: none;
}

/* Estilos para los iconos de acciones */
v-icon {
  color: #66b2ff;
  font-size: 1.2em;
  cursor: pointer;
}

v-icon:hover {
  color: #99ccff;
}

.action-icon {
  color: #66b2ff;
  font-size: 1.2em;
  cursor: pointer;
}
</style>
