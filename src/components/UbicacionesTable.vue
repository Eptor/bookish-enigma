<template>
  <v-container>
    <v-data-table
      :headers="headers"
      :items="ubicaciones"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Ubicaciones</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="dialogCrearUbicacion = true">Crear Ubicación</v-btn>
        </v-toolbar>
      </template>
      <template v-slot:item.imagen="{ item }">
        <v-img :src="item.imagen" max-height="100" max-width="100"></v-img>
      </template>
      <template v-slot:item.action="{ item }">
        <v-icon small @click="eliminarUbicacion(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>

    <!-- Diálogo para crear ubicación -->
    <v-dialog v-model="dialogCrearUbicacion" max-width="600px">
      <v-card>
        <v-card-title>
          Crear Nueva Ubicación
        </v-card-title>
        <v-card-text>
          <v-form @submit.prevent="crearUbicacion">
            <v-text-field v-model="nuevaUbicacion.nombre" label="Nombre" required></v-text-field>
            <v-text-field v-model="nuevaUbicacion.descripcion" label="Descripción" required></v-text-field>
            <v-text-field v-model="nuevaUbicacion.imagen" label="URL de Imagen" required></v-text-field>
            <v-btn type="submit" color="primary">Crear</v-btn>
            <v-btn color="primary" @click="cancelarCrearUbicacion">Cancelar</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      headers: [
        { title: 'ID', value: 'id' },
        { title: 'Nombre', value: 'nombre' },
        { title: 'Descripción', value: 'descripcion' },
        { title: 'Imagen', value: 'imagen' },
        { title: 'Acciones', value: 'action', sortable: false },
      ],
      ubicaciones: [], // Inicializamos como un array vacío
      dialogCrearUbicacion: false,
      nuevaUbicacion: {
        nombre: '',
        descripcion: '',
        imagen: '',
      },
    };
  },
  mounted() {
    // Llamada para obtener datos del backend
    this.fetchUbicaciones();
  },
  methods: {
    async fetchUbicaciones() {
      try {
        const response = await axios.get('http://localhost:8000/ubicaciones');
        if (Array.isArray(response.data)) {
          this.ubicaciones = response.data;
        } else {
          console.log('Respuesta inválida desde el servidor:', response.data);
        }
      } catch (error) {
        console.error('Error fetching ubicaciones:', error);
      }
    },

    async crearUbicacion() {
      try {
        const response = await axios.post('http://localhost:8000/ubicaciones', this.nuevaUbicacion);
        console.log('Ubicación creada:', response.data);
        
        // Verificar si la respuesta es un objeto vacío {}
        if (Object.keys(response.data).length === 0 && response.data.constructor === Object) {
          console.log('La respuesta del servidor es un objeto vacío {}.');
          // Podemos manejar esta situación de acuerdo a las necesidades del frontend
        } else {
          this.ubicaciones.push(response.data);
        }

        // Limpiar los campos de nuevaUbicacion y cerrar el diálogo
        this.nuevaUbicacion = {
          nombre: '',
          descripcion: '',
          imagen: '',
        };
        this.dialogCrearUbicacion = false;

      } catch (error) {
        console.error('Error creando ubicación:', error);
      }
    },

    async eliminarUbicacion(ubicacion) {
      try {
        await axios.delete(`http://localhost:8000/ubicaciones/${ubicacion.id}`);
        this.ubicaciones = this.ubicaciones.filter(u => u.id !== ubicacion.id);
      } catch (error) {
        console.error('Error eliminando ubicación:', error);
      }
    },

    cancelarCrearUbicacion() {
      // Limpiar los campos de nuevaUbicacion y cerrar el diálogo
      this.nuevaUbicacion = {
        nombre: '',
        descripcion: '',
        imagen: '',
      };
      this.dialogCrearUbicacion = false;
    },
  },
};
</script>

<style scoped>
/* Estilos específicos para este componente */
</style>
