<template>
  <div class="lessonset">
    <div v-for="(classes, index) in sortedClasses()" :key="index">
      <fieldset>
        <!-- classes information -->
        <img v-bind:src="classes.img" alt="" height="300px" width="300px" />
        <h2 v-text="classes.subject"></h2>
        <p>Price: {{ classes.price }}</p>
        <p>Location: {{ classes.location }}</p>
        <p>Spaces: {{ classes.dSpace }}</p>
        <!-- Add to Cart Button -->
        <button
          v-on:click="addToCart(classes)"
          v-bind:disabled="!canAddToCart(classes)"
        >
          <!-- icon to add to cart -->
          <i class="fas fa-shopping-cart"> </i> Add to Cart
        </button>
      </fieldset>
    </div>
  </div>
</template>
<script>
export default {
  name: "ProductList",
  props: ["cart", "classes", "sortedClasses"],
  methods: {
    addToCart(product) {
      this.$emit("addProduct", product);
    },
    canAddToCart(product) {
      return this.$parent.canAddToCart(product);
    },
  },
};
</script>