<script setup>
import { ref } from 'vue'

const clienteId = ref('')
const ordenId = ref('')
const error = ref('')
const resultado = ref(null)

// Lista de pedidos 
const orders = [
  {
    client_id: 10,
    client_name: 'Lue Luettgen',
    order_id: 11,
    status: 'delivered',
    total: 34.36,
    products: [
      {
        id: 1,
        name: 'Prof. Dimitri Keebler',
        price: 8.59,
        quantity: 4,
        subtotal: 34.36
      }
    ]
  },
  {
    client_id: 11,
    client_name: 'Muriel Rowe III',
    order_id: 20,
    status: 'shipped',
    total: 76.92,
    products: [
      {
        id: 10,
        name: 'Sienna Kuhlman',
        price: 76.92,
        quantity: 1,
        subtotal: 76.92
      }
    ]
  },
  {
    client_id: 4,
    client_name: 'Eleanor Thompson',
    order_id: 15,
    status: 'delivered',
    total: 120.45,
    products: [
      {
        id: 15,
        name: 'Smartphone X1000',
        price: 120.45,
        quantity: 1,
        subtotal: 120.45
      }
    ]
  },
]

// Función de búsqueda flexible
function buscarOrden() {
  error.value = ''
  resultado.value = null

  if (!clienteId.value && !ordenId.value) {
    error.value = 'Debes ingresar al menos un ID.'
    return
  }

  const match = orders.find(order => {
    const clienteCoincide = clienteId.value && parseInt(clienteId.value) === order.client_id
    const ordenCoincide = ordenId.value && parseInt(ordenId.value) === order.order_id

    if (clienteId.value && ordenId.value) {
      return clienteCoincide && ordenCoincide
    } else if (clienteId.value) {
      return clienteCoincide
    } else if (ordenId.value) {
      return ordenCoincide
    }
    return false
  })

  if (match) {
    resultado.value = match
  } else {
    error.value = 'No se encontró ninguna orden que coincida con los datos ingresados.'
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
        <span :class="'status ' + resultado.status">{{ resultado.status }}</span>
      </p>
      <p><strong>Total:</strong> ${{ resultado.total.toFixed(2) }}</p>

      <h3>Productos</h3>
      <ul class="product-list">
        <li v-for="product in resultado.products" :key="product.id" class="product-item">
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
 body {
    background-color: #eed590; 

    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
    color: #333;
  }

.app {
  padding: 2rem;
  font-family: Arial, sans-serif;
  max-width: 700px;
  margin: 0 auto;
}

.form {
  margin-bottom: 1.5rem;
}

input {
  margin-right: 10px;
  padding: 5px;
  border-radius: 4px;
  border: 1px solid #ccc;
}

button {
  padding: 6px 12px;
  border: none;
  background-color: #e99075;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #cd725d;
}

.error {
  color: red;
  margin-top: 1rem;
}

.order-card {
  padding: 1.5rem;
  background-color: #ffebd5;
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.status {
  padding: 0.2rem 0.6rem;
  border-radius: 4px;
  color: #fff;
  font-weight: bold;
  text-transform: capitalize;
}

.status.delivered {
  background-color: #38a169;
}

.status.shipped {
  background-color: #38bdf8;
}

.product-list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
}

.product-item {
  border-top: 1px solid #eee;
  padding: 1rem 0;
}

.product-item:first-child {
  border-top: none;
}

.name {
  font-weight: 600;
  font-size: 1.1rem;
}

.details {
  color: #555;
}
</style>
