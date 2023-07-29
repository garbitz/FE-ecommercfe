<template>
    <div>
      <!-- Show the product card -->
      <div v-if="product">
        <div :class="['product-card', categoryClass]">
          <img :src="product.image" :alt="product.title" v-if="!unavailableCategory" />
          <div class="product-details" v-if="!unavailableCategory">
            <h2 :class="categoryTitleClass">{{ product.title }}</h2>
            <p>{{ product.category }}</p>
              <div class="product-rating">
              <!-- Show the rating as stars -->
              <div class="stars">
                <span :style="{ width: (product.rating.rate * 20) + '%' }"></span>
              </div>
            </div>
            <p>{{ product.description }}</p>
            <h2 :class="categoryPriceClass">${{ product.price }}</h2>
            <div class="product-buttons">
              <button @click="buyNow">Buy Now</button>
              <button @click="nextProduct">Next Product</button>
            </div>
          </div>
          <p class="unavailable-message" v-else>
            Product is unavailable to show
          </p>
          <button @click="nextProduct" v-if="unavailableCategory && !product.available">
            Next Product
          </button>
        </div>
      </div>
  
      <div v-else>
        <div class="product-card">
          <!-- The spinner is now part of the card content -->
          <div class="spinner"></div>
        </div>    
      </div>
    </div>
  </template>
  
  
  
  <script>
  import axios from "axios";
  
  export default {
    name: "App",
    data() {
      return {
        list: [],
        currentProductId: null,
        product: null,
        maxProductId: 20,
        unavailableCategory: false, // Flag to track if the category is unavailable
      };
    },
  
    async mounted() {
      let result = await axios.get("https://fakestoreapi.com/products");
      this.list = result.data;
  
      // Show the first product card when the component is mounted
      if (this.list.length > 0) {
        this.currentProductId = this.list[0].id;
        this.fetchProduct();
      }
    },
  
    methods: {
      fetchProduct() {
        axios
          .get(`https://fakestoreapi.com/products/${this.currentProductId}`)
          .then((response) => {
            const product = response.data;
            this.product = product; // Set the product data
  
            // Check if the product category is "Men's Clothing"
            if (product.category === "men's clothing") {
              document.body.classList.add("men-clothing-bg"); // Add class to body for men's clothing background gradient
              document.body.classList.remove("women-clothing-bg"); // Remove class for women's clothing background gradient
              this.unavailableCategory = false; // Reset the flag for unavailable category
            } else if (product.category === "women's clothing") {
              document.body.classList.add("women-clothing-bg"); // Add class to body for women's clothing background gradient
              document.body.classList.remove("men-clothing-bg"); // Remove class for men's clothing background gradient
              this.unavailableCategory = false; // Reset the flag for unavailable category
            } else {
              document.body.classList.remove("men-clothing-bg"); // Remove class for men's clothing background gradient
              document.body.classList.remove("women-clothing-bg"); // Remove class for women's clothing background gradient
              this.unavailableCategory = true; // Set the flag for unavailable category
            }
          })
          .catch((error) => {
            console.error("Error fetching product:", error);
          });
      },
  
      buyNow() {
        // Implement your buy now functionality here
      },
  
      nextProduct() {
        this.currentProductId = (this.currentProductId % this.maxProductId) + 1;
        this.fetchProduct();
      },
    },
  
    computed: {
      categoryClass() {
        if (!this.product) {
          return ""; // No product, so no specific style class
        } else if (this.product.category === "men's clothing") {
          this.unavailableCategory = false; // Reset the flag for men's clothing category
          return "men-clothing-style"; // Men's Clothing category
        } else if (this.product.category === "women's clothing") {
          this.unavailableCategory = false; // Reset the flag for women's clothing category
          return "women-clothing-style"; // Women's Clothing category
        } else {
          this.unavailableCategory = true; // Set the flag for unavailable category
          return "unavailable-style";
        }
      },
      categoryTitleClass() {
        return this.product && this.product.category === "men's clothing" ? "men-clothing-title" : "";
      },
      categoryPriceClass() {
        return this.product && this.product.category === "women's clothing" ? "women-clothing-price" : "";
      },
    },
  };
  </script>
  
<style src="@/assets/style/page.css">
</style>
  
  