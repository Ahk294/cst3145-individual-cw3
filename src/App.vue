<template>
  <div id="app">
    <!-- navbar -->
    <nav class="navbar fixed-top navbar-expand-lg navbar-dark bg-dark py-3">
      <div class="container">
        <a href="#" v-on:click="showCheckoutPage" class="navbar-brand">After School Activities</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav mb-2 mb-lg-0 w-100">
            <li class="nav-item ms-auto me-auto w-75">
              <div class="d-flex">
                <!-- disables the search field if the user is on checkout page -->
                <input v-if="showLessonPage" v-on:keyup="search" v-model="searchTerm" class="form-control" type="search"
                  placeholder="Search" aria-label="Search" />
                <input v-else disabled v-on:keyup="search" v-model="searchTerm" class="form-control" type="search"
                  placeholder="Search" aria-label="Search" />
              </div>
            </li>
            <li class="nav-item">
              <a v-if="showLessonPage" class="nav-link active" data-bs-toggle="offcanvas"
                data-bs-target="#offcanvasFilter" aria-controls="offcanvasFilter">
                <i class="fa-solid fa-filter"></i> Show Filter
              </a>
              <a v-else disabled class="nav-link" data-bs-toggle="offcanvas" data-bs-target="#offcanvasFilter"
                aria-controls="offcanvasFilter">
                <i class="fa-solid fa-filter"></i> Show Filter
              </a>
            </li>
            <li class="nav-item">
              <!-- disables the button if the cart length is 0 -->
              <button :disabled="cart.length == 0" v-on:click="showCheckoutPage" v-if="showLessonPage"
                class="btn btn-outline-light" type="submit">
                <i class="fa-solid fa-cart-shopping"></i> Checkout ({{ cart.length }})
              </button>
              <button v-on:click="showCheckoutPage" v-else class="btn btn-outline-light" type="submit">
                <i class="fa-solid fa-home"></i> Home
              </button>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- offcanvas -->
    <div class="offcanvas offcanvas-start" tabindex="-1" id="offcanvasFilter" aria-labelledby="offcanvasFilterLabel">
      <div class="offcanvas-header">
        <h5 class="offcanvas-title display-5" id="offcanvasFilterLabel">Filter</h5>
        <button type="button" class="btn-close text-reset" data-bs-dismiss="offcanvas" aria-label="Close"></button>
      </div>
      <div class="offcanvas-body text-start">
        <div>
          <p>Filter your results by selecting one of the following options</p>
          <div class="form-check">
            <input v-model="sortBy" v-bind:value="'subject'" class="form-check-input" type="radio" name="filter"
              id="subject" checked>
            <label class="form-check-label" for="subject">
              Subject
            </label>
          </div>
          <div class="form-check">
            <input v-model="sortBy" v-bind:value="'location'" class="form-check-input" type="radio" name="filter"
              id="location">
            <label class="form-check-label" for="location">
              Location
            </label>
          </div>
          <div class="form-check">
            <input v-model="sortBy" v-bind:value="'price'" class="form-check-input" type="radio" name="filter" id="price">
            <label class="form-check-label" for="price">
              Price
            </label>
          </div>
          <div class="form-check">
            <input v-model="sortBy" v-bind:value="'spaces'" class="form-check-input" type="radio" name="filter"
              id="spaces">
            <label class="form-check-label" for="spaces">
              Availability
            </label>
          </div>
        </div>
        <hr>
        <div>
          <div class="form-check">
            <input v-model="sortRule" v-bind:value="'ascending'" class="form-check-input" type="radio" name="filterRule"
              id="ascending" checked>
            <label class="form-check-label" for="ascending">
              Ascending
            </label>
          </div>
          <div class="form-check">
            <input v-model="sortRule" v-bind:value="'descending'" class="form-check-input" type="radio" name="filterRule"
              id="descending">
            <label class="form-check-label" for="descending">
              Descending
            </label>
          </div>
        </div>
      </div>
    </div>

    <!-- modal for order confirmation -->
    <div class="modal fade" id="orderConfirm" tabindex="-1" aria-labelledby="orderConfirmLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="orderConfirmLabel">Order Confirmed</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <p>Your order has been placed! Thank you and visit again!</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">Close</button>
            <button type="button" class="btn btn-dark" data-bs-dismiss="modal">Okay</button>
          </div>
        </div>
      </div>
    </div>

    <!-- content -->
    <div class="container mt-5">
      <div v-if="searchStatus">
        <div class="row">
          <!-- v-text directive will be used to update the text with the value provided for data key 'sitename' -->
          <h1 class="display-5 ms-4 mt-5" v-if="searchArr.length != 1">Displaying {{ searchArr.length }} Results</h1>
          <h1 class="display-5 ms-4 mt-5" v-else>Displaying {{ searchArr.length }} Result</h1>
          <div id="lessons" class="col-12 col-md-6 col-lg-4" v-for="lesson in searchArr" :key="lesson.id">
            <!-- lesson component -->
            <lesson :lesson="lesson" :cart="cart"></lesson>
          </div>
        </div>
      </div>
      <div v-else>
        <!-- lesson page -->
        <div v-if="showLessonPage">
          <div class="row">
            <!-- v-text directive will be used to update the text with the value provided for data key 'sitename' -->
            <h1 class="display-5 text-center mt-5" v-text="sitename"></h1>
            <div id="lessons" class="col-12 col-md-6 col-lg-4" v-for="lesson in sortedLessons" :key="lesson.id">
              <!-- lesson component -->
              <lesson :lesson="lesson" :cart="cart"></lesson>
            </div>
          </div>
        </div>

        <!-- cart and checkout page -->
        <div v-else>
          <div class="row">
            <h1 class="display-5 ms-4 mt-5">Number of items in cart: {{ cart.length }}</h1>
            <div class="col-12 col-md-6 col-lg-4" v-for="(lesson, index) in cart" :key="index + lesson.id">
              <!-- checkout component -->
              <checkout :lessons="lessons" :lesson="lesson" :cart="cart" :show-lesson-page="showLessonPage"
                :search-term="searchTerm"></checkout>
            </div>
          </div>
          <hr>
          <h1 class="display-6 text-center pt-2">Checkout</h1>
          <order-form :name="name" :phone="phone" :cart="cart"></order-form>
        </div>
      </div>
    </div>

    <!-- footer -->
    <div class="footer text-center p-4 bg-secondary">
      <a style="text-decoration: none" class="text-white fw-bold"
        href="https://www.linkedin.com/in/abdul-haseeb-khan-b1020815a/">Abdul Haseeb Khan - M00792907</a>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import Lesson from './components/Lesson.vue';
import Checkout from './components/Checkout.vue';
import OrderForm from "./components/OrderForm.vue";

import lessons from "./lessons.json";

export default {
  name: 'App',
  components: {
    Lesson,
    Checkout,
    OrderForm
  },
  // Initial data has been declared as a function
  data() {
    return {
      sitename: "After School Activities",
      sortBy: 'subject',
      sortRule: 'ascending',
      name: '',
      phone: '',
      searchTerm: '',
      showLessonPage: true,
      searchStatus: false,
      cartLength: false,
      lessons: lessons,
      cart: [],
      searchArr: [],
    };
  },
  methods: {
    search() {
      // search function that loops through the lesson cards and looks for the term typed in the search bar
      this.searchArr = [];
      this.searchStatus = true;
      let term = this.searchTerm.toLowerCase();
      let lessons = this.lessons;

      // if nothing is searched, then the normal home page is shown
      if (term == "") {
        this.searchStatus = false;
      }

      /* if the term/s typed in the searchbar are found in the 'subject' or 'location' of a lesson,
         then that lesson's object is pushed into the searchArr so that it can be rendered on the search page
      */
      for (let i = 0; i < lessons.length; i++) {
        if (lessons[i].subject.toLowerCase().includes(term.toLowerCase()) || lessons[i].location.toLowerCase().includes(term.toLowerCase())) {
          this.searchArr.push(lessons[i]);
        }
      }
    },

    showCheckoutPage() {
      // terinary operation to toggle checkout page
      this.showLessonPage = this.showLessonPage ? false : true;

      // condition to allow the user to retain their serach reults page for better user experience
      if (this.showLessonPage === true && this.searchTerm) {
        this.searchStatus = true;
      } else {
        this.searchStatus = false;
      }
    },
  },
  // Computed properties are added.
  computed: {
    // sorting the lessons
    sortedLessons() {
      let lessonsArr = this.lessons.slice(0);

      // sorting price
      if (this.sortBy === 'price') {
        return this.sortRule === 'ascending' ?
          lessonsArr.sort((a, b) => { return a.price - b.price; }) :
          lessonsArr.sort((a, b) => { return b.price - a.price; });
      }

      // sorting spaces
      if (this.sortBy === 'spaces') {
        return this.sortRule === 'ascending' ?
          lessonsArr.sort((a, b) => { return a.spaces - b.spaces; }) :
          lessonsArr.sort((a, b) => { return b.spaces - a.spaces; });
      }

      // sorting subject
      if (this.sortBy === 'subject') {
        if (this.sortRule === 'ascending') {
          return lessonsArr.sort((a, b) => (a.subject > b.subject) ? 1 : -1);
        } else {
          return lessonsArr.sort((a, b) => (a.subject < b.subject) ? 1 : -1);
        }
      }

      // sorting location
      if (this.sortBy === 'location') {
        if (this.sortRule === 'ascending') {
          return lessonsArr.sort((a, b) => (a.location > b.location) ? 1 : -1);
        } else {
          return lessonsArr.sort((a, b) => (a.location < b.location) ? 1 : -1);
        }
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
