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
      <template v-slot:item.action="{ item }">
        <v-icon small @click="eliminarResponsable(item)">mdi-delete</v-icon>
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
        { title: 'Acciones', value: 'action', sortable: false },
      ],
      responsables: [],
      dialogCrearResponsable: false,
      nuevoResponsable: {
        numeroEmpleado: '',
        nombre: '',
        imagen: '',
      },
    };
  },
  mounted() {
    this.fetchResponsables();
  },
  methods: {
    async fetchResponsables() {
      try {
        const response = await axios.get('http://localhost:8000/responsables');
        this.responsables = response.data;
      } catch (error) {
        console.error('Error fetching responsables:', error);
      }
    },

    async crearResponsable() {
      try {
        const response = await axios.post('http://localhost:8000/responsables', this.nuevoResponsable);
        this.responsables.push(response.data);
        this.dialogCrearResponsable = false;
        this.resetNuevoResponsable();
      } catch (error) {
        console.error('Error creating responsable:', error);
      }
    },

    async eliminarResponsable(responsable) {
      try {
        await axios.delete(`http://localhost:8000/responsables/${responsable.id}`);
        this.responsables = this.responsables.filter(r => r.id !== responsable.id);
      } catch (error) {
        console.error('Error deleting responsable:', error);
      }
    },

    cancelarCrearResponsable() {
      this.resetNuevoResponsable();
      this.dialogCrearResponsable = false;
    },

    resetNuevoResponsable() {
      this.nuevoResponsable = {
        numeroEmpleado: '',
        nombre: '',
        imagen: '',
      };
    },
  },
};
</script>

<style scoped>
/* Estilos específicos para este componente */
</style>
