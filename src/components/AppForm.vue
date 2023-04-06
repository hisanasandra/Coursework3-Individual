<template>
  <div>
    <strong>My Cart:</strong>
    <div v-for="(classes, index) in showCart()" :key="index">
      <!-- classes information -->

      <h2 v-text="classes"></h2>

      <button @click="removeClass(classes)">Remove Item</button>
    </div>

    <div class="checkOutBox">
      <!--Check Out Details  -->
      <h2>CheckOut</h2>
      <p>
        <strong> Name: </strong>
        <input v-model.trim="order.name" required />
      </p>
      <p>
        <strong> Phone: </strong>
        <input v-model.trim="order.phone" type="number" required />
      </p>

      <!-- Order Button for placing order -->
      <button @click="submitForm">PLACE ORDER</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "checkoutForm",
  props: ["cart", "showCart", "classes", "order"],
  methods: {
    removeClass(classes) {
      this.$emit("removeClass", classes);
    },
    showCart() {
      return this.$parent.showCart();
    },
    submitForm() {
      this.$emit("submitForm");
    },
  },
};
</script>
