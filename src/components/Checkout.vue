<template>
    <div class="card m-4">
        <img v-bind:src="lesson.image" class="card-img-top" />
        <!-- div containing lesson details -->
        <div class="card-body">
            <!-- {{ propertyName }} Moustache syntax will derive value from 'subject' attribute of 'lesson' object -->
            <h4 class="card-title text-center">{{ lesson.subject }}</h4>
            <hr />
            <!-- Similarly through moustache syntax, different values will be injected of the 'lesson' object -->
            <p class="card-text">
                <i class="fa-solid fa-location-dot"></i> Location:
                {{ lesson.location }}
            </p>
            <p class="card-text">
                <i class="fa-solid fa-tag"></i> Price: AED {{ lesson.price }}
            </p>

            <div class="d-flex">
                <!-- Add to cart button -->
                <!-- v-on:click directive is used to add a click event handler that will trigger the 'removeFromCart' method. -->
                <button v-on:click="removeLesson(lesson._id, index)" id="btn_remove" class="btn btn-outline-danger ms-auto">
                    Remove
                </button>
            </div>
        </div>
    </div>
</template>

<script>
/* eslint-disable */
export default {
    name: 'Checkout',
    props: ['lesson', 'index', 'cart'],
    methods: {
        // removes object based on attribute, 'id' in this case
        removeLesson(idValue, index) {
            this.$emit('removeFromCart', idValue, index);
        },
    }
};
</script>

<style></style>