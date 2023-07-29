<template>
  <div>
    <!-- Show the product card -->
    <div v-if="product">
      <div :class="['product-card', categoryClass]">
        <img :src="product.image" :alt="product.title" v-if="!unavailableCategory" />
        <div class="product-details" v-if="!unavailableCategory">
          <h2 :class="categoryTitleClass">{{ product.title }}</h2>
          <p>Category: {{ product.category }}</p>
          <p>Rating: {{ product.rating.rate }}</p>
          <p>{{ product.description }}</p>
          <h2 :class="categoryPriceClass">{{ product.price }}</h2>
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

<style>
/* Your other CSS styles */

body {
  background: linear-gradient(180deg, #dcdcdc 60%, #fff 60%); /* Men's Clothing gradient background */
  margin: 0; /* Remove default body margin */
  padding: 0; /* Remove default body padding */
}

body.men-clothing-bg {
  background: linear-gradient(180deg, #d6e6ff 60%, #fff 60%); /* Men's Clothing gradient background */
}

body.women-clothing-bg {
  background: linear-gradient(180deg, #fde2ff 60%, #fff 60%); /* Women's Clothing gradient background */
}

.product-card {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  padding: 30px; /* Adjust the padding as needed */
  width: 800px; /* Set the fixed width for the product card */
  height: 600px; /* Set the fixed height for the product card */
  overflow: hidden; /* Ensure that content doesn't overflow */
  display: flex;
}

.product-card img {
  max-width: 250px; /* Adjust the image width as needed */
  height: auto;
  margin-right: 20px;
  border-radius: 5px;
  object-fit: cover; /* Prevent the image from stretching */
  flex-shrink: 0; /* Prevent the image from shrinking */
}

.product-details {
  flex: 1; /* Fill the available space on the right side */
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.product-category-rating {
  color: #444;
  font-size: 14px;
  margin: 0;
  flex-shrink: 0; /* Prevent the category and rating from shrinking */
}

.product-description {
  margin: 10px 0;
  flex: 1; /* Fill the available space, but allow scrolling */
}

.product-price {
  margin: 0;
  flex-shrink: 0; /* Prevent the price from shrinking */
}

.product-card.men-clothing-style {
  background-color: #f3f3f3; /* Light gray background for Men's Clothing */
}

.product-card.women-clothing-style {
  background-color: #f3f3f3; /* Light purple background for Women's Clothing */
}

.product-card.men-clothing-style h2 {
  flex-shrink: 0; /* Prevent the title from shrinking */
  color: #002772;
  font-weight: bold;
  font-size: 20px;
  margin: 0;
  margin-bottom: 5px;
}

.product-card.women-clothing-style h2 {
  flex-shrink: 0; /* Prevent the title from shrinking */
  color: #720060;
  font-weight: bold;
  font-size: 20px;
  margin: 0;
  margin-bottom: 5px;
}

.product-card.unavailable-style {
  display: flex;
  flex-direction: column;
  align-items: center; /* Center the content horizontally */
  justify-content: center; /* Center the content vertically */
}

.product-card.unavailable-style .unavailable-message {
  font-weight: bold;
  font-size: 18px;
  color: #ff0000; /* Red color for the unavailable message */
  
}

.product-card.unavailable-style button {
  margin-top: 10px; /* Add some spacing between the message and the button */
}


.product-title {
  margin: 0;
  margin-bottom: 5px;
}

.product-title.men-clothing-title {
  color: #002772;
  font-weight: bold;
  font-size: 20px;
}

.product-title.women-clothing-title {
  color: #720060;
  font-weight: bold;
  font-size: 20px;
}

.product-price.men-clothing-price {
  color: #002772;
  font-weight: bold;
  font-size: 18px;
}

.product-price.women-clothing-price {
  color: #720060;
  font-weight: bold;
  font-size: 18px;
}

.unavailable-message {
  font-weight: bold;
  font-size: 18px;
  color: #ff0000; /* Red color for the unavailable message */
}

.spinner {
  position: absolute; /* Position the spinner absolutely within the card */
  top: 50%; /* Move the spinner 50% from the top of the card */
  left: 50%; /* Move the spinner 50% from the left of the card */
  transform: translate(-50%, -50%); /* Center the spinner both horizontally and vertically */
  border: 5px solid rgba(0, 0, 0, 0.3);
  border-top: 5px solid #002772; /* Set the spinner color (blue) */
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 2s linear infinite; /* Create the spinner animation */
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}



</style>

