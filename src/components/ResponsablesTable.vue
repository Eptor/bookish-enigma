<template>
  <v-container>
    <v-data-table :headers="headers" :items="responsables" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Responsables</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="dialogCrearResponsable = true">Crear Responsable</v-btn>
        </v-toolbar>
      </template>
      <template v-slot:item.imagen="{ item }">
        <v-img :src="item.imagen" max-height="100" max-width="100"></v-img>
      </template>
    </v-data-table>

    <!-- Diálogo para crear responsable -->
    <v-dialog v-model="dialogCrearResponsable" max-width="600px">
      <v-card>
        <v-card-title>
          Crear Nuevo Responsable
        </v-card-title>
        <v-card-text>
          <v-form @submit.prevent="crearResponsable">
            <v-text-field v-model="nuevoResponsable.numeroEmpleado" label="Número de Empleado" required></v-text-field>
            <v-text-field v-model="nuevoResponsable.nombre" label="Nombre" required></v-text-field>
            <v-text-field v-model="nuevoResponsable.imagen" label="URL de Imagen" required></v-text-field>
            <v-btn type="submit" color="primary">Crear</v-btn>
            <v-btn color="primary" @click="cancelarCrearResponsable">Cancelar</v-btn>
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
        { title: 'Número de Empleado', value: 'numeroEmpleado' },
        { title: 'Nombre', value: 'nombre' },
        { title: 'Imagen', value: 'imagen' },
      ],
      responsables: [], // Inicializamos como un array vacío
      dialogCrearResponsable: false,
      nuevoResponsable: {
        numeroEmpleado: '',
        nombre: '',
        imagen: '',
      },
    };
  },
  mounted() {
    // Llamada para obtener datos del backend
    this.fetchResponsables();
  },
  methods: {
    async fetchResponsables() {
      try {
        const response = await axios.get('http://localhost:8000/responsables');
        this.responsables = response.data;
        console.log('Responsables:', this.responsables);
      } catch (error) {
        console.error('Error fetching responsables:', error);
      }
    },

    async crearResponsable() {
      try {
        const response = await axios.post('http://localhost:8000/responsables', this.nuevoResponsable);
        console.log('Responsable creado:', response.data);
        this.responsables.push(response.data);
        this.dialogCrearResponsable = false;

        this.nuevoResponsable = {
          numeroEmpleado: '',
          nombre: '',
          imagen: '',
        };
      } catch (error) {
        console.error('Error creando responsable:', error);
      }
    },

    cancelarCrearResponsable() {
      // Limpiar los campos de nuevoResponsable y cerrar el diálogo
      this.nuevoResponsable = {
        numeroEmpleado: '',
        nombre: '',
        imagen: '',
      };
      this.dialogCrearResponsable = false;
    },
  },
};
</script>

<style scoped>
/* Estilos específicos para este componente */
</style>
