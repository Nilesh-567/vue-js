<!-- App.vue -->
<template>
  <div id="app">
    <h1>Simple E-commerce Store</h1>
    <div v-for="product in products" :key="product.id" class="product">
      <h2>{{ product.name }}</h2>
      <p>Price: ₹{{ product.price }}</p>
      <input
        type="number"
        v-model.number="product.quantity"
        placeholder="Quantity"
        min="0"
      />
    </div>
    <button @click="addToCart">Add to Cart</button>
    <div v-if="cart.length > 0">
      <h2>Shopping Cart</h2>
      <ul>
        <li v-for="item in cart" :key="item.id">
          {{ item.name }} - Quantity: {{ item.quantity }} - Total: ₹{{ item.price * item.quantity }}
        </li>
      </ul>
      <h3>Total Amount: ₹{{ totalAmount }}</h3>
      <button @click="generateQRCode">Proceed to Pay</button>
    </div>
    <div v-if="showQRCode">
      <h2>Scan QR Code to Pay</h2>
      <qrcode-vue :value="upiLink" :size="200" />
    </div>
  </div>
</template>

<script>
import QrcodeVue from "qrcode.vue";

export default {
  components: {
    QrcodeVue,
  },
  data() {
    return {
      products: [
        { id: 1, name: "Product A", price: 20, quantity: 0 },
        { id: 2, name: "Product B", price: 10, quantity: 0 },
        { id: 3, name: "Product C", price: 30, quantity: 0 },
      ],
      cart: [],
      totalAmount: 0,
      showQRCode: false,
      upiLink: "",
    };
  },
  methods: {
    addToCart() {
      this.cart = this.products
        .filter((product) => product.quantity > 0)
        .map((product) => ({
          id: product.id,
          name: product.name,
          price: product.price,
          quantity: product.quantity,
        }));
      this.calculateTotal();
    },
    calculateTotal() {
      this.totalAmount = this.cart.reduce(
        (sum, item) => sum + item.price * item.quantity,
        0
      );
    },
    generateQRCode() {
      if (this.totalAmount > 0) {
        this.upiLink = `upi://pay?pa=6299695907-3@ybl&pn=YourName&am=${this.totalAmount}&cu=INR`;
        this.showQRCode = true;
      } else {
        alert("Your cart is empty!");
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  max-width: 600px;
  margin: 20px auto;
}
.product {
  margin-bottom: 20px;
}
button {
  margin-top: 10px;
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}
</style>
