<template>
  <div class="store-container">
    <!-- Header -->
    <header class="store-header">
      <h1>Algonquin Pet Store</h1>
      <p class="tagline">Meals for Pets, Lessons for Students!</p>
    </header>

    <!-- Images -->
    <div class="animal-images">
      <img src="pictures/dog.jpg" alt="Dog" />
      <img src="pictures/cat.jpg" alt="Cat" />
      <img src="pictures/bird.jpg" alt="Bird" />
    </div>

    <!-- Products -->
    <div v-if="products.length">
      <h2>Select a Product</h2>

      <div
        v-for="product in products"
        :key="product.id"
        class="product-item"
      >
        <input
          type="radio"
          :id="product.id"
          v-model="selectedProduct"
          :value="product"
        />
        <label :for="product.id">
          <strong>{{ product.name }}</strong>
          – ${{ product.price.toFixed(2) }}
        </label>
      </div>

      <!-- Quantity -->
      <div v-if="selectedProduct" class="quantity-container">
        <label>Quantity:</label>
        <input type="number" v-model="quantity" min="1" />
      </div>

      <!-- Total -->
      <div v-if="selectedProduct" class="total-price">
        <h3>Total: ${{ totalPrice.toFixed(2) }}</h3>
      </div>

      <button
        class="order-button"
        :disabled="!selectedProduct || quantity <= 0"
        @click="submitOrder"
      >
        Place Order
      </button>
    </div>

    <!-- Loading -->
    <div v-else>
      <p>Loading products...</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "OrderForm",

  data() {
    return {
      products: [],
      selectedProduct: null,
      quantity: 1,

      // ✅ ENV-BASED URLs (THIS IS THE KEY FIX)
      productServiceUrl: process.env.VUE_APP_PRODUCT_SERVICE_URL,
      orderServiceUrl: process.env.VUE_APP_ORDER_SERVICE_URL,
    };
  },

  async created() {
    await this.fetchProducts();
  },

  computed: {
    totalPrice() {
      return this.selectedProduct
        ? this.selectedProduct.price * this.quantity
        : 0;
    },
  },

  methods: {
    async fetchProducts() {
      try {
        const response = await fetch(
          `${this.productServiceUrl}/products`
        );

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }

        this.products = await response.json();
      } catch (err) {
        console.error("Error fetching products:", err);
        alert("Failed to fetch products.");
      }
    },

    async submitOrder() {
      try {
        const response = await fetch(
          `${this.orderServiceUrl}/orders`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              product: this.selectedProduct,
              quantity: this.quantity,
              totalPrice: this.totalPrice,
            }),
          }
        );

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }

        alert("Order placed successfully!");
      } catch (err) {
        console.error("Order failed:", err);
        alert("Failed to place order.");
      }
    },
  },
};
</script>

<style scoped>
.store-container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 10px;
}

.store-header {
  text-align: center;
}

.animal-images {
  display: flex;
  justify-content: space-around;
  margin: 20px 0;
}

.animal-images img {
  width: 180px;
  height: 180px;
  border-radius: 50%;
}

.product-item {
  margin: 10px 0;
}

.quantity-container {
  margin-top: 10px;
}

.total-price {
  margin-top: 15px;
}

.order-button {
  margin-top: 20px;
  width: 100%;
  padding: 10px;
  background: #42b983;
  color: white;
  border: none;
  cursor: pointer;
}

.order-button:disabled {
  background: #ccc;
}
</style>