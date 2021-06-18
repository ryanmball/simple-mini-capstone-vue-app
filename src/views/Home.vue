<template>
  <div class="home">
    <div>
      <h3>Create new product:</h3>
      <input
        type="text"
        v-model="newProductParams.name"
        placeholder="name"
      /><br />
      <input
        type="text"
        placeholder="description"
        v-model="newProductParams.description"
      /><br />
      <input
        type="text"
        placeholder="price"
        v-model="newProductParams.price"
      /><br />
      <input
        type="text"
        placeholder="image url"
        v-model="newProductParams.image_url"
      /><br />
      <button v-on:click="productCreate()">Add Product</button>
      <p v-if="errors">
        <strong><u>ERRORS:</u></strong>
      </p>
      <p v-for="error in errors" v-bind:key="error">
        <strong>{{ error }}</strong>
      </p>
    </div>
    <datalist id="product-names">
      <option v-for="product in products" v-bind:key="product.id">
        {{ product.name }}
      </option>
    </datalist>
    <h1><u>Products</u></h1>
    <input
      type="text"
      v-model="filter"
      list="product-names"
      placeholder="search"
    />
    <br />
    <select name="order" id="order" v-model="order">
      <option value="">None</option>
      <option value="name">Name</option>
      <option value="created_at">Created</option>
    </select>
    <br />
    <button @click="orderName()">Sort by Name</button>
    <button @click="orderCreated()">Sort by Created</button> <br />
    <button @click="orderClear()">Clear Filter</button>
    <div
      v-for="product in filterBy(
        orderBy(products, order, -1),
        filter,
        'name',
        'description'
      )"
      v-bind:key="product.id"
    >
      <h3>{{ product.name }}</h3>
      <p><img :src="product.image_url" alt="" /></p>
      <button v-on:click="productShow(product)">More Info</button>
      <p>Description: {{ product.description }}</p>
      <p>Price: {{ product.price }}</p>
      <p>Created: {{ product.created_at }}</p>
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
        <button v-on:click="productDestroy()">Delete</button>
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
import Vue2Filters from "vue2-filters";
export default {
  mixins: [Vue2Filters.mixin],
  data: function () {
    return {
      products: [],
      newProductParams: {},
      errors: false,
      currentProduct: "",
      filter: "",
      order: "",
    };
  },
  created: function () {
    this.productIndex();
  },
  methods: {
    productIndex: function () {
      axios.get("http://localhost:3000/products").then((response) => {
        console.log("Recipes array", response.data);
        this.products = response.data;
      });
    },
    productCreate: function () {
      console.log(this.newProductParams);
      axios
        .post("http://localhost:3000/products", this.newProductParams)
        .then((response) => {
          console.log("Product added!", response.data);
          this.products.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
      this.newProductParams = {};
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
    productDestroy: function () {
      axios
        .delete(`http://localhost:3000/products/${this.currentProduct.id}`)
        .then((response) => {
          console.log(response.data);
          var index = this.products.indexOf(this.currentProduct);
          this.products.splice(index, 1);
        });
    },
    orderName: function () {
      this.order = "name";
    },
    orderCreated: function () {
      this.order = "created_at";
    },
    orderClear: function () {
      this.order = "";
      this.filter = "";
    },
  },
};
</script>
