<template>
    <div class="row">
        <div class="mb-3 col-12 col-md-6 col-lg-6">
            <label for="name" class="form-label">Name</label>
            <input v-model="name" type="text" class="form-control" id="name" placeholder="Fullname">
        </div>
        <div class="mb-3 col-12 col-md-6 col-lg-6">
            <label for="phone" class="form-label">Phone</label>
            <input v-model="phone" type="number" class="form-control" id="phone" placeholder="971501234567">
        </div>
        <div class="mb-3 d-flex justify-content-center">
            <button v-if="validateForm" id="btn_checkout" class="btn btn-success" data-bs-toggle="modal"
                data-bs-target="#orderConfirm">
                Checkout
            </button>
            <button disabled v-else id="btn_checkout" class="btn btn-success">
                Checkout
            </button>
        </div>
    </div>
</template>

<script>
/* eslint-disable */
export default {
    name: 'OrderForm',
    props: ['name', 'phone', 'cart'],
    computed: {
        // using regex with the test() method to validate the checkout form
        validateForm() {
            return /^(?!\s*$)[a-zA-Z.+\s'-]+$/.test(this.name) && /^(?!\s*$)[0-9.+\s'-]+$/.test(this.phone) && this.phone.length === 10 && this.cart.length > 0;
        },
    }
};
</script>

<style></style>