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
        <v-icon small @click="editarUbicacion(item)">mdi-pencil</v-icon>
        <v-icon small @click="eliminarUbicacion(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>

    <!-- Diálogo para crear ubicación -->
    <v-dialog v-model="dialogCrearUbicacion" max-width="600px">
      <v-card>
        <v-card-title>
          {{ modoEdicion ? 'Editar Ubicación' : 'Crear Nueva Ubicación' }}
        </v-card-title>
        <v-card-text>
          <v-form @submit.prevent="modoEdicion ? guardarEdicion() : crearUbicacion()">
            <v-text-field v-model="nuevaUbicacion.nombre" label="Nombre" required></v-text-field>
            <v-text-field v-model="nuevaUbicacion.descripcion" label="Descripción" required></v-text-field>
            <v-text-field v-model="nuevaUbicacion.imagen" label="URL de Imagen" required></v-text-field>
            <v-btn type="submit" color="primary">{{ modoEdicion ? 'Guardar Cambios' : 'Crear' }}</v-btn>
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
      ubicaciones: [],
      dialogCrearUbicacion: false,
      nuevaUbicacion: {
        nombre: '',
        descripcion: '',
        imagen: '',
      },
      modoEdicion: false,
      ubicacionIdEnEdicion: null,
    };
  },
  mounted() {
    this.fetchUbicaciones();
  },
  methods: {
    async fetchUbicaciones() {
      try {
        const response = await axios.get('http://localhost:8000/ubicaciones');
        this.ubicaciones = response.data;
      } catch (error) {
        console.error('Error fetching ubicaciones:', error);
      }
    },

    async crearUbicacion() {
      try {
        const response = await axios.post('http://localhost:8000/ubicaciones', this.nuevaUbicacion);
        this.ubicaciones.push(response.data);
        this.dialogCrearUbicacion = false;
        this.resetNuevaUbicacion();
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
      this.resetNuevaUbicacion();
      this.dialogCrearUbicacion = false;
      this.modoEdicion = false;
    },

    resetNuevaUbicacion() {
      this.nuevaUbicacion = {
        nombre: '',
        descripcion: '',
        imagen: '',
      };
      this.ubicacionIdEnEdicion = null;
    },

    editarUbicacion(ubicacion) {
      this.modoEdicion = true;
      this.ubicacionIdEnEdicion = ubicacion.id;
      this.nuevaUbicacion = { ...ubicacion };
      this.dialogCrearUbicacion = true;
    },

    async guardarEdicion() {
      try {
        await axios.put(`http://localhost:8000/ubicaciones/${this.ubicacionIdEnEdicion}`, this.nuevaUbicacion);
        const index = this.ubicaciones.findIndex(u => u.id === this.ubicacionIdEnEdicion);
        if (index !== -1) {
          this.ubicaciones.splice(index, 1, this.nuevaUbicacion);
        }
        this.dialogCrearUbicacion = false;
        this.resetNuevaUbicacion();
        this.modoEdicion = false;
      } catch (error) {
        console.error('Error guardando ubicación editada:', error);
      }
    },
  },
};
</script>

<style scoped>
/* Estilos específicos para este componente */
</style>
