<template>
  <div></div>
  <h1>Client</h1>
  <Them @load="loadProducts"></Them>
  <table border="1px">
    <tr>
      <th>Name</th>
      <th>Price</th>
      <th>Quantity</th>
      <th>Action</th>
    </tr>
    <tr v-for="product in products" :key="product.id">
      <td>{{ product.name }}</td>
      <td>{{ product.price }}</td>
      <td>{{ product.quantity }}</td>
      <td>
        <button @click="editProduct(product)">Edit</button>
        <button @click="deleteProduct(product.id)">Delete</button>
      </td>
    </tr>
  </table>

  <div v-if="isEditing">
    <h2>Edit Product</h2>
    <form @submit.prevent="handleEdit">
      <p>Name</p>
      <input type="text" v-model="currentProduct.name" required>
      <p>Price</p>
      <input type="number" v-model="currentProduct.price" required>
      <p>Quantity</p>
      <input type="number" v-model="currentProduct.quantity" required>
      <button type="submit">Update</button>
      <button @click="cancelEdit">Cancel</button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Them from './components/Them.vue';
import axios from 'axios';

const products = ref([]);
const isEditing = ref(false);
const currentProduct = ref({ id: null, name: '', price: 0, quantity: 0 });

// Function to fetch products
const fetchProducts = async () => {
  try {
    const response = await axios.get("http://localhost:8080/products");
    products.value = response.data;
  } catch (error) {
    console.error("Error fetching products:", error);
  }
};

// Load products when the component is mounted
const loadProducts = () => {
  fetchProducts();
};

// Function to edit a product
const editProduct = (product) => {
  isEditing.value = true;
  currentProduct.value = { ...product }; // Copy the product to the currentProduct
};

// Function to handle edit submission
const handleEdit = async () => {
  try {
    await axios.put(`http://localhost:8080/products/${currentProduct.value.id}`, currentProduct.value);
    await fetchProducts(); // Refresh the product list
    isEditing.value = false; // Close the edit form
    currentProduct.value = { id: null, name: '', price: 0, quantity: 0 }; // Reset the current product
  } catch (error) {
    console.error("Error updating product:", error);
  }
};

// Function to delete a product
const deleteProduct = async (id) => {
  try {
    await axios.delete(`http://localhost:8080/products/${id}`);
    await fetchProducts(); // Refresh the product list
  } catch (error) {
    console.error("Error deleting product:", error);
  }
};

onMounted(fetchProducts);
</script>
