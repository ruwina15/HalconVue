<script setup>
import { ref } from "vue";

const clienteId = ref("");
const ordenId = ref("");
const error = ref("");
const resultado = ref(null);

async function buscarOrden() {
  error.value = "";
  resultado.value = null;

  if (!clienteId.value || !ordenId.value) {
    error.value = "Debes ingresar ClienteID y OrdenID.";
    return;
  }

  try {
    const res = await fetch(
      `http://localhost:8000/api/order?clientId=${clienteId.value}&orderId=${ordenId.value}`
    );
    if (!res.ok) {
      if (res.status === 404) {
        error.value = "Orden no encontrada.";
      } else {
        error.value = `Error al obtener la orden: ${res.status} ${res.statusText}`;
      }
      return;
    }
    const data = await res.json();
    resultado.value = data;
  } catch (err) {
    error.value = "Error de red: " + err.message;
  }
}
</script>

<template>
  <div class="app">
    <h1>Buscar orden del cliente</h1>

    <div class="form">
      <label for="clienteId">ID Cliente:</label>
      <input v-model="clienteId" type="text" id="clienteId" />

      <label for="ordenId">ID Orden:</label>
      <input v-model="ordenId" type="text" id="ordenId" />

      <button @click="buscarOrden">Buscar</button>
    </div>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-if="resultado" class="order-card">
      <h2>Pedido de {{ resultado.client_name }}</h2>
      <p><strong>ID de Orden:</strong> {{ resultado.order_id }}</p>
      <p>
        <strong>Estado:</strong>
        <span :class="'status ' + resultado.status">{{
          resultado.status
        }}</span>
      </p>
      <p><strong>Total:</strong> ${{ resultado.total.toFixed(2) }}</p>

      <h3>Dirección de envío</h3>
      <div class="address">
        <p>
          {{ resultado.address.street }}
          {{ resultado.address.external_number }},
          {{ resultado.address.colony }}
        </p>
        <p>
          {{ resultado.address.city }}, {{ resultado.address.state }}
          {{ resultado.address.zip_code }}, {{ resultado.address.country }}
        </p>
      </div>

      <h3>Productos</h3>
      <ul class="product-list">
        <li
          v-for="product in resultado.products"
          :key="product.id"
          class="product-item"
        >
          <div class="name">{{ product.name }}</div>
          <div class="details">
            {{ product.quantity }} × ${{ product.price.toFixed(2) }} =
            <strong>${{ product.subtotal.toFixed(2) }}</strong>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap");
* {
  box-sizing: border-box;
}
html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}
body {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f0f2f5;
  font-family: "Open Sans", Arial, sans-serif;
  color: #333;
}
.app {
  width: 100%;
  max-width: 800px;
  padding: 2rem;
  margin: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}
h1 {
  text-align: center;
  color: #343a40;
  margin-bottom: 2rem;
}
.form {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 2rem;
}
.form label {
  font-weight: 600;
}
.form input {
  width: 120px;
  padding: 8px 12px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  background: #fff;
  transition: border-color 0.2s;
}
.form input:focus {
  border-color: #80bdff;
  outline: none;
}
button {
  padding: 8px 16px;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
}
button:hover {
  background-color: #0056b3;
}
.error {
  color: #d9534f;
  font-weight: 600;
  text-align: center;
  margin-top: 1rem;
}
.order-card {
  background: #fff;
  width: 100%;
  border-radius: 8px;
  padding: 2rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-top: 2rem;
}
.order-card h2 {
  margin-top: 0;
  color: #007bff;
}
h3 {
  margin-top: 1.5rem;
  margin-bottom: 0.75rem;
  color: #343a40;
}
.status {
  padding: 0.3rem 0.8rem;
  border-radius: 12px;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
.status.delivered {
  background-color: #28a745;
}
.status.shipped {
  background-color: #17a2b8;
}
.product-list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
}
.product-item {
  display: flex;
  justify-content: space-between;
  padding: 0.75rem 0;
  border-bottom: 1px solid #e9ecef;
}
.product-item:last-child {
  border-bottom: none;
}
.name {
  font-weight: 600;
  font-size: 1.1rem;
  color: #343a40;
}
.details {
  color: #6c757d;
}
.address {
  background: #f8f9fa;
  padding: 1rem;
  border-radius: 4px;
  margin: 1rem 0 1.5rem;
}
.address p {
  margin: 0.2rem 0;
  color: #495057;
}
</style>
