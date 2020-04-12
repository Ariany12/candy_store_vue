<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p><button v-on:click="addProduct()"> add a new product</button></p>
      
       Name:<input type="text" v-model="newProductName">
      <p>Price:<input type="text" v-model="newProductPrice"></p>
      <P>Flavor:<input type="text" v-model="newProductFlavor"></p>
      <P>Image_url:<input type="text" v-model="newProductImage_url"></P>
      <hr>
    <div v-bind:key="product.id" v-for="product in products">
      <p>Id: {{product.id}}</p>
      <p>Name: {{product.name}}</p>
      <p>Price: {{product.price}}</p>
      <p>Flavor: {{product.flavor}}</p>
    <button v-on:click="showInfo(product)"> Show more info </button>
        <div v-if="currentProduct === product">
        <p>image_url: {{product.image_url}}</p>
        <img v-bind:src="product.image_url" style="width:250px;height:200px;">  

      <p>Name:<input type="text" v-model="product.name"></p>
      <p>Price:<input type="text" v-model="product.price"></p>
      <P>Flavor:<input type="text" v-model="product.flavor"></p>
      <P>Image_url:<input type="text" v-model="product.image_url"></P>
      <p><button v-on:click="updateProduct(product)"> update product</button></p>
      <p><button v-on:click="deleteProduct(product)"> delete a product</button></p>

      </div>  
      <hr>
    </div>
  </div>
</template>
 
<style>
</style>

<script>
import axios from "axios"

export default {
  data: function() {
    return {
      message: "Candy Store",
      products: [],
      currentProduct: {},
      newProductName: "",
      newProductPrice: "",
      newProductFlavor: "",
      newProductImage_url: "",
      errors: []

    };
  },
  created: function() {
     console.log('show all candies')
     axios.get("/api/products").then(response => {
       console.log(response)
      this.products = response.data;
     })
  },
  methods: {
    showInfo: function(theProduct){
      console.log('show image')
      this.currentProduct = theProduct;
    },
    addProduct: function(){
      console.log(this.newProductName)
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        flavor: this.newProductFlavor,
        image_url: this.newProductImage_url
      }
      axios.post("/api/products", params).then(response => {
        console.log(response.data);
        this.products.push(response.data)
        this.newProductTitle = " " ;
        this.newProductPrice = " " ;
        this.newProductFlavor = " " ;
        this.newProductImage_url = " "; 
      })
      .catch(error => {
        console.log(error.response.data.errors);
        this.errors = error.response.data.errors;
      });
    },
    updateProduct: function(theProduct){
      console.log('here')
      var params = {
        name: theProduct.name,
        price: theProduct.price,
        flavor: theProduct.flavor,
        image_url: theProduct.image_url
    }
    axios.patch(`/api/products/${theProduct.id}`, params).then(response => {
        console.log(response.data);
        this.products.push(response.data)
      })
    },
    deleteProduct: function(theProduct){
      console.log('delete product')
      //make the web request to rails
      axios.delete('/api/products/' + theProduct.id).then(response =>{
      console.log(response.data)
      //delete in the web page
      //find the index
      var index = this.products.indexOf(theProduct)
      // then delete
      this.products.splice(index, 1)
      })
    }
  }
};

</script>
