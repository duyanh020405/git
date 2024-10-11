<template>
    <div>
        <h1>Bai 1,2,3,4,5,6,7,8,9,10,11,12 // SS23</h1>
        <ul>
            <li v-for="product in products" :key="product.id">
                {{ product.name }} - {{ product.email }}
                <button @click="getId(product.id)">Id</button>
                <button @click="deletePr(product.id)">Delete</button>
                <button @click="editProduct(product)">Edit</button>
            </li>
        </ul>

        <!-- Pagination Controls -->
        <div v-if="totalPages > 1">
            <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
            <span>Page {{ currentPage }} of {{ totalPages }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
        </div>

        <div v-if="currentProductId">
            <h2>Selected ID: {{ currentProductId }}</h2>
        </div>
        <div v-else>
            <h2>ID không có</h2>
        </div>

        <h3>{{ isEditMode ? 'Update' : 'Them' }} student</h3>
        <form @submit.prevent="handleSubmit">
            <p>Name</p>
            <input v-model="newStudent.name" type="text" required />

            <p>Email</p>
            <input v-model="newStudent.email" type="email" required />

            <p>Address</p>
            <input v-model="newStudent.address" type="text" />

            <p>Phone</p>
            <input v-model="newStudent.phone" type="text" />

            <p>Date</p>
            <input v-model="newStudent.date" type="date" />

            <button type="submit">{{ isEditMode ? 'Update' : 'Submit' }}</button>
        </form>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const products = ref([]);
const newStudent = ref({
    name: '',
    email: '',
    address: '',
    phone: '',
    date: ''
});

const currentProductId = ref(null);
const isEditMode = ref(false);

// Pagination state
const currentPage = ref(1);
const pageSize = ref(5); // Number of products per page
const totalPages = ref(1);

// Fetch existing students with pagination
const fetchProducts = async () => {
    const response = await axios.get(`http://localhost:8080/student?page=${currentPage.value}&size=${pageSize.value}`);
    products.value = response.data.items; // Assuming response contains an 'items' array
    totalPages.value = Math.ceil(response.data.total / pageSize.value); // Assuming response contains 'total' count
};

// Handle Submit for creating new or updating existing student
const handleSubmit = async () => {
    if (isEditMode.value) {
        await axios.put(`http://localhost:8080/student/${currentProductId.value}`, newStudent.value);
        isEditMode.value = false;
    } else {
        const response = await axios.post("http://localhost:8080/student", newStudent.value);
        products.value.push(response.data);
    }
    resetForm();
    fetchProducts();
};

// Reset form
const resetForm = () => {
    newStudent.value = {
        name: '',
        email: '',
        address: '',
        phone: '',
        date: ''
    };
    isEditMode.value = false;
};

const getId = (id) => {
    const currentUrl = window.location.href;
    const newUrl = new URL(currentUrl);
    newUrl.searchParams.set('id', id);
    window.history.pushState({}, '', newUrl);
    currentProductId.value = id;
};

// Edit existing student
const editProduct = (product) => {
    newStudent.value = { ...product };
    currentProductId.value = product.id;
    isEditMode.value = true;
};

// Delete a product
const deletePr = async (id) => {
    await axios.delete(`http://localhost:8080/student/${id}`);
    fetchProducts();
};

// Pagination controls
const nextPage = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value++;
        fetchProducts();
    }
};

const prevPage = () => {
    if (currentPage.value > 1) {
        currentPage.value--;
        fetchProducts();
    }
};

onMounted(() => {
    fetchProducts();
});

</script>
