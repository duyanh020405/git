<template>
    <div></div>
    <h1>Client</h1>
    <Btvn></Btvn>
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
import Btvn from './Btvn.vue';
import { ref, onMounted } from 'vue';
import Them from './Them.vue';
import axios from 'axios';

const products = ref([]);
const isEditing = ref(false);
const currentProduct = ref({ id: null, name: '', price: 0, quantity: 0 });
const fetchProducts = async () => {
    const response = await axios.get("http://localhost:8080/products");
    products.value = response.data;
};
const loadProducts = () => {
    fetchProducts();
};

const editProduct = (product) => {
    isEditing.value = true;
    currentProduct.value = { ...product };
};

// Function to handle edit submission
const handleEdit = async () => {
    await axios.put(`http://localhost:8080/products/${currentProduct.value.id}`, currentProduct.value);
    await fetchProducts();
    isEditing.value = false;
    currentProduct.value = { id: null, name: '', price: 0, quantity: 0 };

};

const deleteProduct = (id) => {

    axios.delete(`http://localhost:8080/products/${id}`);
    fetchProducts();
};

onMounted(fetchProducts);
</script>
