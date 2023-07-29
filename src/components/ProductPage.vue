<template>
    <div>
      <div v-if="product">
        <div class="product-card">
          <img :src="product.image" :alt="product.title" />
          <h2>{{ product.title }}</h2>
          <p>{{ product.price }}</p>
          <p>{{ product.description }}</p>
          <p>Category: {{ product.category }}</p>
          <p>Rating: {{ product.rating.rate }}</p>
          <button @click="buyNow">Buy Now</button>
          <button @click="nextProduct">Next Product</button>
        </div>
      </div>
      <div v-else>
        <p>Loading...</p>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        product: null,
        currentProductId: 1,
        maxProductId: 20,
      };
    },
    created() {
      this.fetchProduct();
    },
    methods: {
      fetchProduct() {
        axios
          .get(`https://fakestoreapi.com/products/${this.currentProductId}`)
          .then((response) => {
            this.product = response.data;
          })
          .catch((error) => {
            console.error('Error fetching product:', error);
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
  };
  </script>
  
  <style>
  /* Add your CSS styling for the product card here */
  </style>
  