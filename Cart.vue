<template>
  <div>
    <button class="cart-button" @click="openCart">🛒 Cart</button>

    <div v-if="showCart" class="cart-container">
      <div class="container">
        <h1 class="cart-title">Your Cart</h1>
        <table class="cart-table">
          <thead>
            <tr>
              <th>PRODUCT</th>
              <th>PRICE</th>
              <th>QUANTITY</th>
              <th>TOTAL</th>
              <th>REMOVE</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="cart in cartData" :key="cart.id">
              <td>
                <div class="cart-item">
                  <img :src="cart.image" :alt="cart.name" />
                  {{ cart.name }}<br />{{ cart.color }}
                </div>
              </td>
              <td class="price">R{{ cart.price }}</td>
              <td>
                <div class="cart-quantity">
                  <button @click="changeQuantity(cart, -1)" class="quantity-btn">
                    -
                  </button>
                  <input
                    type="number"
                    v-model.number="cart.quantity"
                    class="quantity-input"
                    min="1"
                  />
                  <button @click="changeQuantity(cart, 1)" class="quantity-btn">
                    +
                  </button>
                </div>
              </td>
              <td class="total">R{{ cart.price * cart.quantity }}</td>
              <td class="cart-remove" @click="removeCartItem(cart)">x</td>
            </tr>
          </tbody>
        </table>
        <div class="order-summary">
          <h3>Order Summary</h3>
          <p><span>Subtotal</span> <span>R{{ subtotal }}</span></p>
          <p><span>Shipping</span> <span>Free</span></p>
          <p class="total"><span>Total</span> <span>R{{ subtotal }}</span></p>
          <button @click="goToPayment" class="checkout-btn">CHECKOUT</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      showCart: false,
      cartData: [],
    };
  },
  computed: {
    subtotal() {
      return this.cartData
        .reduce((sum, cart) => sum + cart.price * cart.quantity, 0)
        .toFixed(2);
    },
  },
  methods: {
    openCart() {
      this.showCart = !this.showCart;
    },
    changeQuantity(cart, change) {
      cart.quantity = Math.max(1, cart.quantity + change);
      this.updateCartItem(cart);
    },
    removeCartItem(cart) {
      this.cartData = this.cartData.filter((item) => item.id !== cart.id);
      this.deleteCartItem(cart.id);
    },
    async fetchCartData() {
      try {
        const response = await axios.get('http://localhost:3000/cart');
        this.cartData = response.data;
      } catch (error) {
        console.error('Error fetching cart data:', error);
      }
    },
    async updateCartItem(cart) {
      try {
        await axios.put(`http://localhost:3000/cart/${cart.id}`, cart);
      } catch (error) {
        console.error('Error updating cart item:', error);
      }
    },
    async deleteCartItem(id) {
      try {
        await axios.delete(`http://localhost:3000/cart/${id}`);
      } catch (error) {
        console.error('Error deleting cart item:', error);
      }
    },
    goToPayment() {
      // Use Vue Router to navigate to the payment page.
      this.$router.push('/payment.vue');
      //If you are not using vue-router use window.location.href
      //window.location.href = '/payment';
    },
  },
  mounted() {
    this.fetchCartData();
  },
};
</script>

<style scoped>
/* Add your styles here */
.cart-button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
.cart-container{
  position: fixed;
  top: 50px;
  right: 20px;
  background-color: white;
  border: 1px solid black;
  padding: 20px;
  width: 80%;
  max-width: 800px;
  z-index: 1000;
}
.cart-table {
  width: 100%;
  border-collapse: collapse;
}

.cart-table th,
.cart-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}
.order-summary {
  border: 1px solid #ddd;
  padding: 20px;
  width: 300px; /* Adjust as needed */
}

.order-summary h3 {
  margin-bottom: 15px;
}

.order-summary p {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.order-summary .total {
  font-weight: bold;
  border-top: 1px solid #eee;
  padding-top: 10px;
}

.checkout-btn {
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: white;
  text-align: center;
  text-decoration: none;
  border: none;
  cursor: pointer;
  margin-top: 15px;
}

.checkout-btn:hover {
  background-color: #0056b3;
}
</style>
  <style scoped>
  .cart-button {
    cursor: pointer;
    padding: 10px 20px;
    background-color: #f0f0f0;
    border: 1px solid #ccc;
  }
  
  .cart-container {
    position: fixed;
    top: 50px;
    right: 20px;
    background-color: white;
    border: 1px solid #ccc;
    padding: 20px;
    width: 600px;
    max-height: 80vh;
    overflow-y: auto;
    z-index: 1000;
  }
  
  .cart-table {
    width: 100%;
    border-collapse: collapse;
  }
  
  .cart-table th,
  .cart-table td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
  }
  
  .cart-item {
    display: flex;
    align-items: center;
  }
  
  .cart-item img {
    width: 50px;
    height: 50px;
    margin-right: 10px;
  }
  
  .cart-quantity {
    display: flex;
    align-items: center;
  }
  
  .quantity-btn {
    padding: 5px 10px;
    cursor: pointer;
  }
  
  .quantity-input {
    width: 40px;
    text-align: center;
    margin: 0 5px;
  }
  
  .cart-remove {
    cursor: pointer;
    text-align: center;
  }
  
  .order-summary {
    margin-top: 20px;
  }
  
  .order-summary p {
    display: flex;
    justify-content: space-between;
  }
  
  .checkout-btn {
    display: block;
    text-align: center;
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    text-decoration: none;
    margin-top: 10px;
  }
  </style>