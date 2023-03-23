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
                <button v-on:click="removeFromCart(lesson.id)" id="btn_remove" class="btn btn-outline-danger ms-auto">
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
    props: ['lessons', 'lesson', 'cart', 'showLessonPage', 'searchTerm'],
    methods: {
        // removes object based on attribute, 'id' in this case
        removeFromCart(idValue) {
            let index;
            let arr = this.cart;
            let lessons = this.lessons;

            // finding the index of the object/lesson to be removed
            for (let i = 0; i < arr.length; i++) {
                if (arr[i]["id"] === idValue) {
                    index = i;
                }
            }

            // adding back 1 space to the lesson that was removed from cart
            for (let i = 0; i < lessons.length; i++) {
                if (lessons[i]["id"] === idValue) {
                    lessons[i].spaces = lessons[i].spaces + 1;
                }
            }
            arr.splice(index, 1);

            // condition to automatically direct user to the home page when there are no items in the cart
            if (this.cart.length === 0) {
                this.showLessonPage = true;
                this.searchTerm = '';
            }
        },
    }
};
</script>

<style></style>