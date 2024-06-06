<template>
  <v-container>
    <v-data-table :headers="headers" :items="activos" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Activos</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="dialog = true">Nuevo Activo</v-btn>
        </v-toolbar>
      </template>
      <template v-slot:item.imagen="{ item }">
        <v-img :src="item.imagen" max-height="100" max-width="100"></v-img>
      </template>
    </v-data-table>

    <v-dialog v-model="dialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Nuevo Activo</span>
        </v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-text-field v-model="nuevoActivo.numeroSerie" label="Número de Serie"></v-text-field>
            <v-text-field v-model="nuevoActivo.numeroInventarioUABC" label="Número de Inventario UABC"></v-text-field>
            <v-text-field v-model="nuevoActivo.tipo" label="Tipo"></v-text-field>
            <v-text-field v-model="nuevoActivo.descripcion" label="Descripción"></v-text-field>
            <v-text-field v-model="nuevoActivo.imagen" label="URL de la Imagen"></v-text-field>
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">Cancelar</v-btn>
          <v-btn color="blue darken-1" text @click="crearActivo">Guardar</v-btn>
        </v-card-actions>
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
        { text: 'ID', value: 'id' },
        { text: 'Número de Serie', value: 'numeroSerie' },
        { text: 'Número de Inventario UABC', value: 'numeroInventarioUABC' },
        { text: 'Tipo', value: 'tipo' },
        { text: 'Descripción', value: 'descripcion' },
        { text: 'Imagen', value: 'imagen' },
      ],
      activos: [],
      dialog: false,
      nuevoActivo: {
        numeroSerie: '',
        numeroInventarioUABC: '',
        tipo: '',
        descripcion: '',
        imagen: ''
      },
    };
  },
  mounted() {
    this.fetchActivos();
  },
  methods: {
    async fetchActivos() {
      try {
        const response = await axios.get('http://localhost:8000/activos');
        // Asegurarse de que response.data es un array
        this.activos = Array.isArray(response.data) ? response.data : [];
      } catch (error) {
        console.error('Error fetching activos:', error);
        this.activos = []; // Asegurarse de que activos es un array incluso si hay un error
      }
    },
    async crearActivo() {
      try {
        await axios.post('http://localhost:8000/activos', this.nuevoActivo);
        this.fetchActivos();
        this.dialog = false;
        this.nuevoActivo = { // Reset form
          numeroSerie: '',
          numeroInventarioUABC: '',
          tipo: '',
          descripcion: '',
          imagen: ''
        };
      } catch (error) {
        console.error('Error creating activo:', error);
      }
    }
  },
};
</script>

<style scoped>
/* Aquí puedes agregar estilos específicos para este componente */
</style>
