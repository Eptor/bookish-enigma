<template>
  <v-container>
    <v-data-table
      :headers="headers"
      :items="responsables"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Responsables</v-toolbar-title>
        </v-toolbar>
      </template>
      <template v-slot:item.imagen="{ item }">
        <v-img :src="item.imagen" max-height="100" max-width="100"></v-img>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Número de Empleado', value: 'numeroEmpleado' },
        { text: 'Nombre', value: 'nombre' },
        { text: 'Imagen', value: 'imagen' },
      ],
      responsables: [], // Aquí irán los datos que obtendrás del backend
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
        console.log('RESPONSABLES');
        console.log(this.responsables);
      } catch (error) {
        console.error('Error fetching responsables:', error);
      }
    },
  },
};
</script>

<style scoped>
/* Estilos específicos para este componente */
</style>
