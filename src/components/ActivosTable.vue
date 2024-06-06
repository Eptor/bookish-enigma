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
      <template v-slot:item.nombre="{ item }">
        <span>{{ item.numeroSerie }}</span>
      </template>
      <template v-slot:item.ubicacion="{ item }">
        <span>{{ item.Ubicacion.nombre }}</span>
      </template>
      <template v-slot:item.responsable="{ item }">
        <span>{{ item.Responsable.nombre }}</span>
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

            <!-- Agregar selección de Ubicación -->
            <v-select :items="ubicaciones" item-title="nombre" item-value="id" v-model="nuevoActivo.ubicacionId" label="Ubicaciones"></v-select>
            

            <!-- Agregar selección de Responsable -->
            <v-select :items="responsables" item-title="nombre" item-value="id" v-model="nuevoActivo.responsableId" label="Responsables"></v-select>
            
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
import { ref, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const activos = ref([]);
    const dialog = ref(false);
    const nuevoActivo = ref({
      numeroSerie: '',
      numeroInventarioUABC: '',
      tipo: '',
      descripcion: '',
      imagen: '',
      ubicacionId: null,
      responsableId: null,
    });
    const ubicaciones = ref([]);
    const responsables = ref([]);

    const headers = [
      { title: 'ID', value: 'id' },
      { title: 'Número de Serie', value: 'numeroSerie' },
      { title: 'Número de Inventario UABC', value: 'numeroInventarioUABC' },
      { title: 'Tipo', value: 'tipo' },
      { title: 'Descripción', value: 'descripcion' },
      { title: 'Imagen', value: 'imagen' },
      { title: 'Ubicación', value: 'Ubicacion.nombre' },
      { title: 'Responsable', value: 'Responsable.nombre' },
    ];

    const fetchActivos = async () => {
      try {
        const response = await axios.get('http://localhost:8000/activos');
        activos.value = Array.isArray(response.data) ? response.data : [];
        console.log('Activos:', activos.value);
      } catch (error) {
        console.error('Error fetching activos:', error);
        activos.value = [];
      }
    };

    const fetchUbicaciones = async () => {
      try {
        const response = await axios.get('http://localhost:8000/ubicaciones');
        ubicaciones.value = response.data.map(ubicacion => ({
          id: ubicacion.id,
          nombre: ubicacion.nombre,
        }));
        console.log('Ubicaciones:', ubicaciones.value);
      } catch (error) {
        console.error('Error fetching ubicaciones:', error);
      }
    };

    const fetchResponsables = async () => {
      try {
        const response = await axios.get('http://localhost:8000/responsables');
        responsables.value = response.data.map(responsable => ({
          id: responsable.id,
          nombre: responsable.nombre,
        }));
        console.log('Responsables:', responsables.value);
      } catch (error) {
        console.error('Error fetching responsables:', error);
      }
    };

    const crearActivo = async () => {
      try {
        console.log('Nuevo Activo:', nuevoActivo.value);
        await axios.post('http://localhost:8000/activos', nuevoActivo.value);
        fetchActivos();
        dialog.value = false;
        nuevoActivo.value = {
          numeroSerie: '',
          numeroInventarioUABC: '',
          tipo: '',
          descripcion: '',
          imagen: '',
          ubicacionId: null,
          responsableId: null,
        };
      } catch (error) {
        console.error('Error creating activo:', error);
      }
    };

    onMounted(() => {
      fetchActivos();
      fetchUbicaciones();
      fetchResponsables();
    });

    return {
      headers,
      activos,
      dialog,
      nuevoActivo,
      ubicaciones,
      responsables,
      crearActivo,
    };
  },
};
</script>

<style scoped>
/* Aquí puedes agregar estilos específicos para este componente */
</style>
