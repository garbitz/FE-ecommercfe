<template>
  <div id="app">
    <!-- Replace the old product card with the ProductDisplay component -->
    <ProductDisplay />
  </div>
</template>

<script>
import ProductDisplay from "@/components/ProductDisplay.vue"; // Import the ProductDisplay component

export default {
  components: {
    ProductDisplay, // Register the ProductDisplay component
  },
};
</script>

<style>
/* Any other CSS styles specific to the App component can be placed here */
</style>
