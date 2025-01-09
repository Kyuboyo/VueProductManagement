<script setup>
import { ref, onMounted } from 'vue';
import ModalComponent from './components/ModalComponent.vue';
import { Icon } from "@iconify/vue";

const products = ref([]);

const newProduct = ref('New Product');
const newPrice = ref(100);

const loadingStatus = ref(false);

const addProductStatus = ref(false);

const openAddProduct = () => {
  addProductStatus.value = true;
};

const editProductStatus = ref(false);
const editProduct = ref('');
const editName = ref('');
const editPrice = ref('');

const closeModal = () => {
  addProductStatus.value = false;
  editProductStatus.value = false;
  editProduct.value = '';
};

onMounted(async () => {
  try {
    const res = await fetch('https://fakestoreapi.com/products');
    const data = await res.json();

    console.log("Products", data);

    products.value = data;
  } catch (err) {
    console.log("Error fetching", err)
  }
  loadingStatus.value = true;
});

const addProduct = () => {
  if (newProduct.value.trim !== '' && newPrice.value.trim !== '') {
    products.value.push({ title: newProduct.value, price: newPrice.value });
    newProduct.value = '';
    newPrice.value = '';
    addProductStatus.value = false;
    closeModal();
  }
};

const removeProduct = (index) => {
  if (Number.isFinite(index)) {
    products.value.splice(index, 1);
  }
};

const editProductFunc = (index, name, price) => {
  editProductStatus.value = true;
  editProduct.value = index;
  editName.value = name;
  editPrice.value = price;
};

const confirmEditFunc = (formData) => {
  console.log("editing ", editProduct.value, formData, formData.title);
  if (editProduct != '') {
    products.value[editProduct.value] = { title: formData.title, price: formData.price };
    editProduct.value = '';
    closeModal();
  }
};
</script>

<template>
  <div style="display: flex; flex-direction: column; align-items: center; min-height: 100vh; width: 100%;">
  <h1>Products</h1>
  <!-- MODAL -->
  <!-- ADD PRODUCT FORM -->
  <ModalComponent :is-open="addProductStatus" title="New Product" price=100 @submit="addProduct" @close="closeModal"></ModalComponent>
  <!-- EDIT PRODUCT FORM -->
  <ModalComponent :is-open="editProductStatus" :title="editName" :price="editPrice" edit=true @submit="confirmEditFunc" @close="closeModal"></ModalComponent>
  
  <!-- ADD BUTTON -->
  <button class="btn btn-primary" style="width: 200px" @click="openAddProduct">Add Product</button>

  <!-- List View -->
  <div v-if="loadingStatus">
  <ol>
    <li v-for="(product, index) in products" :key="index" style="min-width: 100%; margin-top: 10px; margin-bottom: 10px;">
      <div style="width: 100%; display: flex; justify-content: space-between;">
        <!-- Name -->
        <span class="name-text">{{ product.title }}</span> 
        <!-- Price -->
        <span class="price-text name-text">{{ product.price }}</span>
        <!-- Buttons -->
        <span class="btn-container">
          <!-- Edit -->
          <button class="btn btn-secondary btn-icons" @click="editProductFunc(index, product.title, product.price)"><Icon icon="mdi:edit" /></button>
          <!-- Delete -->
          <button class="btn btn-danger btn-icons" @click="removeProduct(index)"><Icon icon="mdi:delete" /></button>
        </span>
      </div>
    </li>
  </ol>
</div>
<div v-else>
  <!-- Loading -->
   <Icon icon="line-md:loading-twotone-loop" style="width: 60px; height: 60px; margin-top: 20px;" />
</div>
</div>
  
</template>

<style scoped>
.btn-icons {
  min-width: 30px;
  min-height: 30px;
  max-width: 30px;
  max-height: 30px;
  padding: 0px;
}

.price-text {
  text-align: center;
}

.name-text {
  min-width: 300px;
  max-width: 300px;
}

.btn-container {
  min-width: 70px;
  max-width: 70px;
  display: flex;
  justify-content: space-between;
}
</style>