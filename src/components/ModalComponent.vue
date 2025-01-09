<script setup>
import { ref, watch } from 'vue';
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
    isOpen: {
        type: Boolean,
        default: false
    },
    title: {
        type: String,
        default: "New Product"
    },
    price: {
        type: Number,
        default: 100
    },
    edit: {
        type: Boolean
    }
});

const formData = ref({ title: "", price: ""});

const emit = defineEmits(['submit', 'close']);

const handleSubmit = () => {
    emit("submit", formData.value)
}
const handleClose = () => {
    emit("close")
}

watch(
    () => ({ title: props.title, price: props.price}),
    (newProps) => {
        formData.value.title = newProps.title;
        formData.value.price = newProps.price;
    },
    { immediate: true }
);
</script>

<template>
        <Teleport to="#modal">
            <div v-if="isOpen" class="modal-overlay">
                <div class="modal-container">
                    <p class="h3">Add Product</p>
                    <form @submit.prevent="handleSubmit" class="modal-content">
                        <div class="input-group input-group-sm mb-3">
                            <span class="input-group-text" id="inputGroup-sizing-sm" style="width: 100px;">Name</span>
                            <input type="text" id="productName" class="form-control" aria-describedby="inputGroup-sizing-sm" v-model="formData.title" required>
                        </div>
                        <div class="input-group input-group-sm mb-3">
                            <span class="input-group-text" id="inputGroup-sizing-sm" style="width: 100px;">Price</span>
                            <input type="text" id="productPrice" class="form-control" aria-describedby="inputGroup-sizing-sm" v-model="formData.price" required>
                        </div>
                        <button class="btn btn-primary modal-button" type="submit" v-if="edit">Edit Product</button>
                        <button class="btn btn-primary modal-button" type="submit" v-else>Add Product</button>
                        <button class="btn btn-secondary modal-button" @click="handleClose">Close</button>
                    </form>
                </div>
            </div>
        </Teleport>
</template>

<style scoped>
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
}

.modal-container {
    background: #444444;
    border-radius: 8px;
    padding: 20px;
    width: 400px;
    max-width: 90%;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.modal-content {
    background: #444444;
    display: flex;
    flex-direction: column;
    border: 0px;
}

.modal-button {
    width: 100%;
    margin-top: 5px;
    margin-bottom: 5px;
}

</style>