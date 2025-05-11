<script setup>
import { ref, watch } from 'vue'
import axios from 'axios'
</script>

<template>
  <div class="app">
    <h1>Buscar orden del cliente</h1>

    <div>
      <label for="clienteId">ID Cliente:</label>
      <input v-model="clienteId" type="text" id="clienteId" />

      <label for="ordenId">ID Orden:</label>
      <input v-model="ordenId" type="text" id="ordenId" />

      <button @click="buscarOrden">Buscar</button>
    </div>

    <div v-if="error" style="color: red;">{{ error }}</div>

    <div v-if="resultado">
      <h2>Resultado</h2>
      <p><strong>ID Cliente:</strong> {{ resultado.idCliente }}</p>
      <p><strong>Nombre Cliente:</strong> {{ resultado.nombreCliente }}</p>
      <p><strong>Número Orden:</strong> {{ resultado.numeroOrden }}</p>
      <p><strong>Método de Pago:</strong> {{ resultado.metodoPago }}</p>
      <p><strong>Precio:</strong> ${{ resultado.precio }}</p>
      <p><strong>Cantidad:</strong> {{ resultado.cantidad }}</p>
      <p><strong>Subtotal:</strong> ${{ resultado.subtotal }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      clienteId: '',
      ordenId: '',
      error: '',
      resultado: null,
    };
  },
  methods: {
    async buscarOrden() {
      this.error = '';
      this.resultado = null;

      try {
        const response = await axios.get(
          `https://tuapi.com/api/ordenes?clienteId=${this.clienteId}&ordenId=${this.ordenId}`
        );

        if (response.data && response.data.length > 0) {
          this.resultado = response.data[0];
        } else {
          this.error = 'No se encontró una orden que coincida con ambos ID.';
        }
      } catch (err) {
        this.error = 'Error al conectar con la API.';
        console.error(err);
      }
    },
  },
};
</script>

<style>
.app {
  padding: 20px;
  font-family: Arial, sans-serif;
}
input {
  margin-right: 10px;
}
</style>
