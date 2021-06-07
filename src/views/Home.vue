<template>
  <div class="home">
    <div>
      <h3>Create new product:</h3>
      <input type="text" v-model="newProductName" placeholder="name" /><br />
      <input
        type="text"
        placeholder="description"
        v-model="newProductDescription"
      /><br />
      <input type="text" placeholder="price" v-model="newProductPrice" /><br />
      <input
        type="text"
        placeholder="image url"
        v-model="newProductImageUrl"
      /><br />
      <button v-on:click="productCreate()">Add Product</button>
      <p v-if="errors">ERRORS:<br />{{ errors }}</p>
    </div>
    <h1><u>Products</u></h1>
    <div v-for="product in products" v-bind:key="product.id">
      <h3>{{ product.name }}</h3>
      <p><img :src="product.image_url" alt="" /></p>
      <button v-on:click="productShow(product)">More Info</button>
      <p>Description: {{ product.description }}</p>
      <p>Price: {{ product.price }}</p>
      <br />
    </div>
    <dialog id="product-details">
      <form method="dialog">
        <h1>Product Info</h1>
        <img :src="currentProduct.image_url" alt="" />
        <p>Name: <input type="text" v-model="currentProduct.name" /></p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>Price: <input type="text" v-model="currentProduct.price" /></p>
        <p>
          Image Url: <input type="text" v-model="currentProduct.image_url" />
        </p>
        <button v-on:click="productUpdate()">Update</button>
        <button>Close</button>
      </form>
    </dialog>
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
      newProductName: "",
      newProductDescription: "",
      newProductPrice: "",
      newProductImageUrl: "",
      errors: false,
      currentProduct: "",
    };
  },
  created: function () {
    this.productIndex();
  },
  methods: {
    productIndex: function () {
      axios.get("http://localhost:3000/products").then((response) => {
        console.log(response.data);
        this.products = response.data;
      });
    },
    productCreate: function () {
      var params = {
        name: this.newProductName,
        description: this.newProductDescription,
        price: this.newProductPrice,
        image_url: this.newProductImageUrl,
      };
      axios
        .post("http://localhost:3000/products", params)
        .then((response) => {
          console.log("Product added!", response.data);
          this.products.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
      this.newProductName = "";
      this.newProductDescription = "";
      this.newProductPrice = "";
      this.newProductImageUrl = "";
    },
    productShow: function (product) {
      console.log(product);
      this.currentProduct = product;
      document.querySelector("#product-details").showModal();
    },
    productUpdate: function () {
      console.log(this.currentProduct);
      var productUpdateParams = {
        name: this.currentProduct.name,
        description: this.currentProduct.description,
        price: this.currentProduct.price,
        image_url: this.currentProduct.image_url,
      };
      axios
        .patch(
          `http://localhost:3000/products/${this.currentProduct.id}`,
          productUpdateParams
        )
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
