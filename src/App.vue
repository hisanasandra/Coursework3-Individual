<template>
  <div id="app">
    <header>
    <h1 v-text="sitename"> </h1>
    </header>
    <button
      v-on:click="showCheckout"
      class="chkoutButton"
      v-bind:disabled="emptyCart(classes)"
    >
      {{ cartItemCount }}
      <i class="fas fa-shopping-cart"></i> CheckOut
    </button>
    <!-- Lessons Page displayed -->
    <div v-if="showClasses">
      <div class="search">
        <p>
          <strong>Search:</strong>
          <input
            type="search"
            v-model="SearchLessons"
            v-on:keyup="showSearch"
            placeholder="Search lessons..."
          />
        </p>
      </div>
      <!-- Sort -->
      <div class="sort">
        <!-- Radio Buttons to choose the parameter for sorting -->
        <strong> Sort By </strong> <br />
        <input type="radio" value="Subject" v-model="sortb" />
        <label for="subject">Subject</label> <br />
        <input type="radio" value="Location" v-model="sortb" />
        <label for="location">Location</label> <br />
        <input type="radio" value="Price" v-model="sortb" />
        <label for="price">Price</label> <br />
        <input type="radio" value="Space" v-model="sortb" />
        <label for="space">Space</label> <br />
        <!-- Radio Buttons to choose the order for sorting -->
        <strong> Order </strong> <br />
        <input type="radio" value="a" v-model="sorto" />
        <label for="a">Ascending</label> <br />
        <input type="radio" value="d" v-model="sorto" />
        <label for="d">Descending</label> <br />
      </div>
      <!-- Searching results -->
      <div v-if="checkBar">
        <div class="lessonset">
          <div v-for="(classes, index) in classesList" :key="index">
            <fieldset>
              <strong> Search Result: </strong>
              <!-- classes information -->
              <img
                v-bind:src="classes.img"
                alt=""
                height="300px"
                width="300px"
              />
              <h2 v-text="classes.subject"></h2>
              <p>Price: {{ classes.price }}</p>
              <p>Location: {{ classes.location }}</p>
              <p>Spaces: {{ classes.dSpace }}</p>
              <!-- Add to Cart Button -->
              <button
                v-on:click="addToCart(classes)"
                v-bind:disabled="!canAddToCart(classes)"
              >
                Add to Cart <i class="fas fa-shopping-cart"></i>
              </button>
            </fieldset>
          </div>
        </div>
      </div>
      <!-- Lessons Display -->
      <product-list
        :cart="cart"
        :classes="classes"
        :sortedClasses="sortedClasses"
        @addProduct="addToCart"
      />
    </div>
    <!-- Checkout Page Display -->
    <div v-else class="myCart">
      <checkout
        :cart="cart"
        :classes="classes"
        :order="order"
        :showCart="showCart"
        @removeClass="removeClass"
        @submitForm="submitForm"
      />
    </div>
    <!-- </main> -->
  </div>
</template>

<script>
import productList from "./components/ProductList.vue";
import checkout from "./components/AppForm.vue";
export default {
  name: "App",
  components: { productList, checkout },
  data() {
    return {
      sitename: "Lessons", //naming the sitename
      classes: [],

      cart: [], // array to store items in cart

      SearchLessons: "", //Array for storing all lesson
      searchInput: "", //to store search input from user
      checkBar: false,

      // cartName: [],

      showClasses: true,
      order: {
        name: "",
        phone: "",
      },
      //to sort according to parameters
      sortb: "Subject",
      sorto: "a",
    };
  },
  methods: {
    //Add to cart method and to check if spaces of the product is more than 0
    addToCart(classes) {
      if (classes.spaces > 0) {
        classes.dSpace -= 1; // confirm there are enough space left in class
      }
      this.cart.push(classes.id);
      console.log(this.cart);
    },
    //To display CheckOut Page

    showCheckout() {
      this.showClasses = this.showClasses ? false : true;
    },
    // To show search results
    showSearch() {
      this.checkBar = true;
    },
    // To place an order
    submitForm() {
      alert("Your Order has been placed");
    },
    // To get the number of times a product is added to cart using its id
    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
    // To check if the particular lesson has space left for more students
    canAddToCart(classes) {
      return classes.spaces > this.cartCount(classes.id);
    },
    // To check if the cart is empty
    emptyCart(classes) {
      return this.cartItemCount == 0;
    },
    // To remove a cart item
    removeClass(classes) {
      for (let j = 0; j < this.classes.length; j++) {
        if (classes === this.classes[j].subject) {
          const index = this.cart.indexOf(this.classes[j].id);
          if (index > -1) {
            this.cart.splice(index, 1);
          }
          this.classes[j].dSpace += 1;
        }
      }
    },
    sortedClasses() {
      let a = 1;
      let b = -1;
      let fn;

      // Setting up ascending and descending algorithm

      if (this.sorto == "a") {
        a = 1;
        b = -1;
      } else if (this.sorto == "d") {
        a = -1;
        b = 1;
      }
      // Calculation if the selected parameter is Subject
      if (this.sortb == "Subject") {
        fn = function (x, y) {
          if (x.subject > y.subject) return a;
          if (x.subject < y.subject) return b;
          return 0;
        };
        // Calculation if the selected parameter is Location
      } else if (this.sortb == "Location") {
        fn = function (x, y) {
          if (x.location > y.location) return a;
          if (x.location < y.location) return b;
          return 0;
        };
        // Calculation if the selected parameter is Price
      } else if (this.sortb == "Price") {
        fn = function (x, y) {
          if (x.price > y.price) return a;
          if (x.price < y.price) return b;
          return 0;
          // Calculation if the selected parameter is Space
        };
      } else if (this.sortb == "Space") {
        fn = function (x, y) {
          if (x.dSpace > y.dSpace) return a;
          if (x.dSpace < y.dSpace) return b;
          return 0;
        };
      }

      return this.classes.sort(fn);
    },
    showCart() {
      let order = [];

      for (let i = 0; i < this.cart.length; i++) {
        for (let j = 0; j < this.classes.length; j++) {
          if (this.cart[i] === this.classes[j].id) {
            //retrieving the id of the items in cart
            order.push(this.classes[j].subject); //pushing the subject for the particular id to order
          }
        }
      }

      return order; //display order
    },
  },
  computed: {
    // Counting the total number of items in cart

    cartItemCount: function () {
      return this.cart.length || "";
    },
    // to display the items in cart at the time of check out

    //Search function
    classesList() {
      this.checkBar = true;
      //to get results while typing characters into the input
      if (this.SearchLessons.trim().length > 0) {
        //Searching the lesson string to identify lessons that matches to the searched input
        return this.classes.filter(
          (classes) =>
            classes.subject
              .toLowerCase()
              .includes(this.SearchLessons.trim().toLowerCase()) ||
            classes.location
              .toLowerCase()
              .includes(this.SearchLessons.trim().toLowerCase())
        );
        // to display all classes if no results were generated from the search
      } else {
        this.checkBar = false;
        this.showClasses = true;
        //To return all the lessons from array to webpage
        return { SearchLessons };
      }
    },
  },
  created: function () {
    const store = this;
    // retrieving data from the server
    fetch("http://localhost:3000/collection/lessons").then(function (response) {
      response.json().then(function (json) {
        // storing the response
        store.classes = json;
      });
    });
  },
};
</script>

<style>
* {
  margin: 0;
  box-sizing: border-box;
  list-style-type: none;
  text-decoration: none;
  font-size: large;
}

header {
  width: 100%;
  height: 10vh;
  text-align: center;
}

h1 {
  padding-top: 4%;
  padding-left: 5%;
  color: #000000;
  font-size: 40px;
}

fieldset {
  border: 5px solid rgb(255, 255, 255);
  border-radius: 3px;
  box-sizing: border-box;
  width: 50%;
  padding: 10px;
  width: 190px;
  height: fit-content;
  margin: inherit;
  float: left;
  /* alignment */
  margin-left: 50px;
  margin-bottom: 100px;
  margin-right: 2px;

  padding-top: 1em;
  padding-bottom: 1em;
  padding-left: 1em;
  padding-right: 1em;
  position: relative;
}

.checkOutBox {
  border: 5px solid rgb(0, 0, 0);
  border-radius: 3px;
  box-sizing: border-box;
  width: 25%;
  padding: 10px;
  height: fit-content;
  margin: auto;
  margin-left: 650px;
  margin-bottom: 20px;
  margin-right: 500px;
  padding-top: 6em;
  padding-bottom: 6em;
  padding-left: 4em;
  padding-right: 6em;
  float: left;
  position: fixed;
}

button {
  background-color: peachpuff;
  border: none;
  color: rgb(0, 0, 0);
  padding: 12px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 11px;
}
/* if button is disabled */
button:disabled {
  background-color: grey;
}

.chkoutButton {
  margin-left: 1300px;
  background-color: peachpuff;
  color: rgb(0, 0, 0);
  size: 18px;
  margin-top: 2%;
}

.sort {
  padding-left: 60px;
  padding-top: 70px;
  padding-bottom: 30px;
  font-size: 20px;
  width: 20%;
  height: 15%;
}

.form-control {
  border: 2px solid black;
}
/* div styling */
.lessonset {
  width: 90%;
  padding-left: 10%;
}

.search {
  padding-left: 60px;
  padding-top: 30px;
  padding-bottom: 30px;
  font-size: 20px;
}

.myCart {
  padding-left: 60px;
  padding-top: 30px;
  padding-bottom: 30px;
  font-size: 20px;
}
</style>