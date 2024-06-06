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
        </v-toolbar>
      </template>
      <template v-slot:item.imagen="{ item }">
        <v-img :src="item.imagen" max-height="100" max-width="100"></v-img>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Nombre', value: 'nombre' },
        { text: 'Descripción', value: 'descripcion' },
        { text: 'Imagen', value: 'imagen' },
      ],
      ubicaciones: [], // Aquí irán los datos que obtendrás del backend
    };
  },
  mounted() {
    // Llamada para obtener datos del backend
    this.fetchUbicaciones();
  },
  methods: {
    async fetchUbicaciones() {
      // Simulación de datos. Reemplaza con la llamada real al backend.
      this.ubicaciones = [
        {
          id: 1,
          nombre: 'Oficina Principal',
          descripcion: 'Edificio A, Planta Baja',
          imagen: 'url_de_imagen',
        },
        // Más datos...
      ];
    },
  },
};
</script>
