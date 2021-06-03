<template>
  <div class="home">
    <div v-for="product in products" v-bind:key="product.id">
      <h3>{{ product.name }}</h3>
      <p>Description: {{ product.description }}</p>
      <p>Price: {{ product.price }}</p>
      <p><img :src="product.image_url" alt="" /></p>
      <br />
    </div>
  </div>
</template>
<style>
img {
  width: 300px;
}
</style>
<script>
import axios from "axios";
export default {
  data: function () {
    return {
      products: [],
    };
  },
  created: function () {
    this.productsIndex();
  },
  methods: {
    productsIndex: function () {
      axios.get("http://localhost:3000/products").then(response => {
        console.log(response.data);
        this.products = response.data;
      });
    },
  },
};
</script>
