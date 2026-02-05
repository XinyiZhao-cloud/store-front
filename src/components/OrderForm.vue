<template>
  <div class="store-container">
    <header class="store-header">
      <h1>Algonquin Pet Store</h1>
      <p class="tagline">Meals for Pets, Lessons for Students!</p>
    </header>

    <div class="animal-images">
      <img src="pictures/dog.jpg" alt="Dog" />
      <img src="pictures/cat.jpg" alt="Cat" />
      <img src="pictures/bird.jpg" alt="Bird" />
    </div>

    <div v-if="products.length">
      <h2>Select a Product</h2>

      <div v-for="product in products" :key="product.id" class="product-item">
        <input
          type="radio"
          :id="product.id"
          v-model="selectedProduct"
          :value="product"
        />
        <label :for="product.id">
          <strong>{{ product.name }}</strong> -
          ${{ product.price ? product.price.toFixed(2) : "N/A" }}
        </label>
      </div>

      <div v-if="selectedProduct" class="quantity-container">
        <label for="quantity">Quantity:</label>
        <input type="number" v-model.number="quantity" min="1" placeholder="Enter quantity" />
      </div>

      <div v-if="selectedProduct" class="total-price">
        <h3>Total Price: ${{ totalPrice.toFixed(2) }}</h3>
      </div>

      <button
        @click="submitOrder"
        :disabled="!selectedProduct || quantity <= 0"
        class="order-button"
      >
        Place Order
      </button>
    </div>

    <div v-else>
      <p>Loading products...</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      selectedProduct: null,
      quantity: 1,
      // Read from Vue env (baked at build time)
      productServiceUrl: process.env.VUE_APP_PRODUCT_SERVICE_URL,
      orderServiceUrl: process.env.VUE_APP_ORDER_SERVICE_URL,
    };
  },

  async created() {
    await this.fetchProducts();
  },

  computed: {
    totalPrice() {
      return this.selectedProduct ? this.selectedProduct.price * this.quantity : 0;
    }
  },

  methods: {
    async fetchProducts() {
      try {
        if (response.ok) {
          this.products = await response.json();
        } else {
          console.error("Product API status:", response.status);
          alert("Failed to fetch products.");
        }
      } catch (error) {
        console.error("Error fetching products:", error);
        alert("Failed to fetch products.");
      }
    },

    async submitOrder() {
      if (!this.selectedProduct || this.quantity <= 0) {
        alert("Please select a product and enter a valid quantity.");
        return;
      }

      try {
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            product: this.selectedProduct,
            quantity: this.quantity,
            totalPrice: this.totalPrice,
          }),
        });

        if (!response.ok) {
          throw new Error(`Server error: ${response.status}`);
        }

        alert(
          `Order for ${this.quantity} x ${this.selectedProduct.name} placed successfully! Total: $${this.totalPrice.toFixed(
            2
          )}`
        );
      } catch (error) {
        console.error("Error placing order:", error);
        alert("Failed to place order.");
      }
    },
  },
};
</script>

<style scoped>
.store-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
}

.store-header {
  text-align: center;
  margin-bottom: 20px;
}

.store-header h1 {
  font-size: 2.5rem;
  color: #42b983;
  margin: 0;
}

.store-header .tagline {
  font-size: 1.2rem;
  color: #777;
  margin-top: 10px;
  font-style: italic;
}

.animal-images {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}

.animal-images img {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  object-fit: cover;
  box-shadow: 0px 2px 8px rgba(0, 0, 0, 0.1);
}

h2,
h3 {
  text-align: center;
  color: #42b983;
}

.product-item {
  display: flex;
  align-items: center;
  margin: 10px 0;
  padding: 10px;
  border: 2px solid #42b983;
  border-radius: 10px;
  background-color: #e3f2fd;
}

.product-item label {
  margin-left: 10px;
  font-size: 1.1rem;
}

.quantity-container {
  margin-top: 15px;
  display: flex;
  align-items: center;
}

.quantity-container label {
  margin-right: 10px;
}

input[type="number"] {
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.total-price {
  margin-top: 20px;
  text-align: center;
  font-size: 1.3rem;
  font-weight: bold;
  color: #ff6f00;
}

.order-button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  width: 100%;
  font-size: 1.1rem;
}

.order-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
