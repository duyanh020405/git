<template>
    <form @submit.prevent="handleSubmit">
        <p>Name</p>
        <input type="text" v-model="newProduct.name">
        <p>Price</p>
        <input type="number" v-model="newProduct.price">
        <p>Quantity</p>
        <input type="text" v-model="newProduct.quantity">
        <button type="submit">Submit</button>
    </form>
</template>
<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue'
const emit = defineEmits(["load"])

const fetchProducts = async () => {
    const response = await axios.get("http://localhost:8080/products");
    products.value = response.data;
};
onMounted(fetchProducts);
const newProduct = ref({
    name: '',
    price: 0,
    quantity: 0
})
const handleSubmit = () => {
    axios.post("http://localhost:8080/products", newProduct.value)
    emit('load');
}
</script>