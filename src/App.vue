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
                <i class="fa-solid fa-cart-shopping"></i> Cart ({{ cart.length }})
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
          <div id="lessons" class="col-12 col-md-6 col-lg-4" v-for="lesson in searchArr" :key="lesson._id">
            <!-- lesson component -->
            <lesson @addToCart="addToCart" :lesson="lesson" :cart="cart"></lesson>
          </div>
        </div>
      </div>
      <div v-else>
        <!-- lesson page -->
        <div v-if="showLessonPage">
          <div class="row">
            <!-- v-text directive will be used to update the text with the value provided for data key 'sitename' -->
            <h1 class="display-5 text-center mt-5" v-text="sitename"></h1>
            <div id="lessons" class="col-12 col-md-6 col-lg-4" v-for="lesson in sortedLessons" :key="lesson._id">
              <!-- lesson component added -->
              <lesson @addToCart="addToCart" :lesson="lesson" :cart="cart"></lesson>
            </div>
          </div>
        </div>

        <!-- cart and checkout page -->
        <div v-else>
          <div class="row">
            <h1 class="display-5 ms-4 mt-5">Number of items in cart: {{ cart.length }}</h1>
            <div class="col-12 col-md-6 col-lg-4" v-for="(lesson, index) in cart" :key="index + ' ' + lesson._id">
              <!-- checkout component added -->
              <checkout @removeFromCart="removeFromCart" :lesson="lesson" :index="index" :cart="cart"></checkout>
            </div>
          </div>
          <hr>
          <h1 class="display-6 text-center pt-2">Checkout</h1>

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
              <button v-if="validateForm" v-on:click="order" id="btn_checkout" class="btn btn-success"
                data-bs-toggle="modal" data-bs-target="#orderConfirm">
                Checkout
              </button>
              <button disabled v-else id="btn_checkout" class="btn btn-success">
                Checkout
              </button>
            </div>
          </div>

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

// importing components
import Lesson from './components/Lesson.vue';
import Checkout from './components/Checkout.vue';

export default {
  name: 'App',
  // declaring the components
  components: {
    Lesson,
    Checkout
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
      lessons: [],
      duplicate_lessons: [],
      cart: [],
      searchArr: [],
    };
  },
  methods: {
    order() {
      // taking only one intsance of each lesson from the cart to create an array of the lessons ordered with no duplication
      let uniqueLessonsArray = [
        ...new Map(this.cart.map((item) => [item["_id"], item])).values(),
      ];

      let orderedLessonsArray = [];

      uniqueLessonsArray.forEach((lesson) => {
        let lessonObj = {};
        this.duplicate_lessons.forEach((item) => {
          if (lesson._id === item._id) {
            lessonObj = {
              id: lesson._id,
              lesson_name: lesson.subject,
              spaces: item.spaces - lesson.spaces,
              rem_spaces: item.spaces - (item.spaces - lesson.spaces)
            };
          }
        });

        orderedLessonsArray.push(lessonObj);
      });

      // creating the order object
      let orderObj = {
        name: this.name,
        phone: this.phone,
        lessons: orderedLessonsArray,
        total_qty: this.cart.length
      };

      // replacing the default '_id' key of the lessons with 'lesson_id' to store in MongoDB 'orders' collection
      orderObj.lessons.forEach((obj) => {
        obj['lesson_id'] = obj['_id'];
        delete obj['_id'];
      });

      // fecth API used to save a new order with POST
      fetch("http://localhost:3000/orders", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(orderObj)
      })
        .then(res => res.json())
        .then(data => {
          // do something with the response data from the PUT request
          console.log({ "Success": data });
        });

      // fetch API to update the number of available spaces in lessons with PUT
      orderedLessonsArray.forEach((lesson) => {
        fetch("http://localhost:3000/lessons/" + lesson['id'], {
          method: "PUT",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ spaces: lesson['rem_spaces'] })
        })
          .then(res => res.json())
          .then(data => {
            // do something with the response data from the PUT request
            console.log({ "Success": data });

            this.cart = [];
          });
      });
    },
    search() {
      // 'search as you type' functionality has been implemented using 'v-model'
      this.searchArr = [];
      this.searchStatus = true;
      let term = this.searchTerm.toLowerCase();

      // if nothing is searched, then the normal home page is shown
      if (term == "") {
        this.searchStatus = false;
      }

      let app = this;

      // fetch API making GET request with the search term
      fetch('http://localhost:3000/lessons/' + term).then(
        function (response) {
          response.json().then(
            function (json) {
              app.searchArr = json;
              console.log(json);
            }
          );
        }
      );
    },
    // add to cart function
    addToCart(lesson) {
      // remove 1 space from the lesson and add it to the cart
      lesson.spaces--;
      this.cart.push(lesson);
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
    // removes object based on attribute, 'id' in this case
    removeFromCart(idValue, index) {
      let arr = this.cart;
      let lessons = this.lessons;

      arr.splice(index, 1);

      // adding back 1 space to the lesson that was removed from cart
      for (let i = 0; i < lessons.length; i++) {
        if (lessons[i]["_id"] == idValue) {
          lessons[i].spaces = lessons[i].spaces + 1;
        }
      }

      // condition to automatically direct user to the home page when there are no items in the cart
      if (this.cart.length === 0) {
        this.showLessonPage = true;
        this.searchTerm = '';
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
    // using regex with the test() method to validate the checkout form
    validateForm() {
      return /^(?!\s*$)[a-zA-Z.+\s'-]+$/.test(this.name) && /^(?!\s*$)[0-9.+\s'-]+$/.test(this.phone) && this.phone.length === 10 && this.cart.length > 0;
    },
  },
  // the created property has the fetch API that retrieves all the lessons with GET
  created: function () {
    console.log('requesting data from server ...');

    let app = this;

    fetch('http://localhost:3000/lessons').then(
      function (response) {
        response.json().then(
          function (json) {
            app.lessons = json;
            console.log(json);
          }
        );
      }
    );

    fetch('http://localhost:3000/lessons').then(
      function (response) {
        response.json().then(
          function (json) {
            app.duplicate_lessons = json;
            console.log(json);
          }
        );
      }
    );
  }
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
