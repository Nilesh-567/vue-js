<!-- src/App.vue -->
<template>
  <div id="app">
    <h1>Simple E-commerce Store</h1>
    
    <!-- Products Section -->
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

    <!-- Cart Section -->
    <div v-if="cart.length > 0">
      <h2>Shopping Cart</h2>
      <ul>
        <li v-for="(item, index) in cart" :key="item.id">
          <strong>{{ item.name }}</strong> - 
          Quantity: <input type="number" v-model.number="item.quantity" @input="updateQuantity(index)" min="1" /> -
          Total: ₹{{ item.price * item.quantity }}
          <button @click="removeFromCart(index)">Remove</button>
        </li>
      </ul>
      <h3>Total Amount: ₹{{ totalAmount }}</h3>
      <button @click="generateQRCode">Proceed to Pay</button>
    </div>

    <!-- QR Code Section -->
    <div v-if="showQRCode">
      <h2>Scan QR Code to Pay</h2>
      <qrcode-vue :value="upiLink" :size="200" />
      <p>Total Amount: ₹{{ totalAmount }}</p>
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
      // Add products with quantity > 0 to the cart
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
    updateQuantity(index) {
      // Recalculate total when quantity changes
      if (this.cart[index].quantity <= 0) {
        this.cart[index].quantity = 1;
      }
      this.calculateTotal();
    },
    removeFromCart(index) {
      // Remove item from cart and recalculate total
      this.cart.splice(index, 1);
      this.calculateTotal();
    },
    calculateTotal() {
      // Recalculate total amount based on cart items
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
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
.product h2 {
  margin: 0;
}
.cart {
  margin-top: 20px;
}
button {
  margin-top: 10px;
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}
.cart button {
  background-color: red;
  color: white;
  border: none;
  padding: 5px;
  border-radius: 3px;
}
.cart ul {
  list-style-type: none;
  padding: 0;
}
.cart li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}
.cart li input {
  width: 50px;
  margin: 0 10px;
}
</style>
