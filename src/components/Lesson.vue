<template>
  <div class="card m-4 lesson">
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
      <p class="card-text">
        <i class="fa-solid fa-user"></i> Available Spaces: {{ lesson.spaces }}
      </p>

      <div class="d-flex">
        <!-- Add to cart button -->
        <!-- v-on:click directive is used to add a click event handler that will trigger the 'addToCart' method. -->
        <!-- v-if directive will only render the button if the 'canAddToCart' method returns True-->
        <button v-on:click="addToCart(lesson)" v-if="canAddToCart(lesson)" id="btn_add"
          class="btn btn-outline-dark ms-auto">
          Add to Cart
        </button>

        <!-- v-else directive will be rendered if the preceding v-if returns False. This will show a disabled button -->
        <button disabled="disabled" v-else class="btn btn-danger ms-auto">
          Add to Cart
        </button>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'Lesson',
  props: ['lesson', 'cart'],
  methods: {
    canAddToCart(lesson) {
      // checks if the lesson has spaces available.
      return lesson.spaces > this.cartCount(lesson.id);
    },
    cartCount(id) {
      // keeps count of the quantity of a particular lesson in the cart
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
    addToCart: function (lesson) {
      // remove 1 space from the lesson and add it to the cart
      lesson.spaces--;
      this.cart.push(lesson);
    },
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
