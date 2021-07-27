<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p>Name: <input type="text" v-model="newProductParams.name" /></p>
    <p>
      Description: <input type="text" v-model="newProductParams.description" />
    </p>
    <p>Image Url: <input type="text" v-model="newProductParams.image_url" /></p>
    <p>Price: <input type="number" v-model="newProductParams.price" /></p>
    <button v-on:click="createProduct()">Add New Product</button>
    <hr />
    <div class="row">
      <div
        class="card col-md-3"
        v-for="product in products"
        v-bind:key="product.id"
      >
        <img v-bind:src="product.image_url" class="card-img-top" alt="..." />
        <div class="card-body">
          <h5 class="card-title">{{ product.name }}</h5>
          <p class="card-text">{{ product.description }}.</p>
          <button class="btn btn-primary" v-on:click="showProduct(product)">
            Update Product
          </button>
        </div>
      </div>
    </div>

    <dialog id="product-details">
      <form method="dialog">
        <p>hello</p>
        <p>Name: <input type="text" v-model="currentProduct.name" /></p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>
          Image_url: <input type="text" v-model="currentProduct.image_url" />
        </p>
        <p>Price: <input type="text" v-model="currentProduct.price" /></p>
        <button v-on:click="updateProduct(currentProduct)">Update</button>
        <button>Close</button>
        <button v-on:click="destroyProduct(currentProduct)">
          Delete Product
        </button>
      </form>
    </dialog>
  </div>
</template>

<style>
img {
  width: 250px;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      products: [],
      newProductParams: {},
      currentProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      console.log("in products index");
      axios.get("http://localhost:3000/products").then((response) => {
        console.log(response.data);
        this.products = response.data;
      });
    },
    createProduct: function () {
      console.log("in product create");
      var productParams = {
        name: this.newProductParams.name,
        description: this.newProductParams.description,
        image_url: this.newProductParams.image_url,
        price: this.newProductParams.price,
      };
      axios
        .post("http://localhost:3000/products", productParams)
        .then((response) => {
          console.log(response.data);
          this.products.push(response.data);
          this.newProductParams = {};
        });
    },
    showProduct: function (theProduct) {
      this.currentProduct = theProduct;
      console.log(theProduct);
      console.log("showing product");
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function (theProduct) {
      console.log("updating....");
      console.log(theProduct);
      var editParams = theProduct;
      axios
        .patch("http://localhost:3000/products/" + editParams.id, editParams)
        .then((response) => {
          console.log(response.data);
        });
    },
    destroyProduct: function (theProduct) {
      console.log("destroying product...");
      axios
        .delete(`http://localhost:3000/products/${theProduct.id}`)
        .then((response) => {
          console.log(response.data);
          var index = this.products.indexOf(theProduct);
          console.log(index);
          this.products.splice(index, 1);
        });
    },
  },
};
</script>
