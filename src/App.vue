<template>
  <div class="cardsWrap">
    <div class="productWrap" :class="currentVariantColor">
      <div class="product-image">
        <img alt="socks" :src="image" />
      </div>
      <div class="product-info">
        <h1>{{ product }}</h1>
        <p v-if="inventory > 10">In stock</p>
        <p v-else-if="inventory <= 10 && inventory > 0">Almost sold out!</p>
        <p v-else>Out of stock</p>
        <p v-if="onSale">On sale</p>
        <ul>
          <li v-for="(detail, index) in details" :key="index">
            {{ detail }}
          </li>
        </ul>
        <div
          v-for="variant in variants"
          :key="variant.variantId"
          @mouseover="updateImage(variant)"
        >
          {{ variant.variantColor }}
        </div>
      </div>
      <button v-on:click="addToCart" class="add-to-cart-button">
        Add to cart
      </button>
      <div class="cart">
        <p>Cart({{ cart }})</p>
      </div>
    </div>
  </div>
  <HomePage />
</template>

<script>
import HomePage from "./components/HomePage.vue";
export default {
  name: "App",
  components: {
    HomePage,
  },
  data() {
    return {
      product: "Socks",
      linkData: "Anchor",
      href: "/",
      image: require("@/assets/blueSocks.png"),
      inventory: 100,
      onSale: true,
      details: ["80% cotton", "20% Polyester", "Gender-neutral"],
      variants: [
        {
          variantColor: "green",
          variantId: "1",
          image: require("@/assets/greenSocks.png"),
        },
        {
          variantColor: "blue",
          variantId: "2",
          image: require("@/assets/blueSocks.png"),
        },
      ],
      cart: 0,
      currentVariantColor: "",
    };
  },
  methods: {
    addToCart() {
      this.cart += 1;
    },
    updateImage(variant) {
      this.image = variant.image;
      this.currentVariantColor = variant.variantColor;
    },
  },
};
</script>

<style>
.cardsWrap {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}
.productWrap {
  display: flex;
  flex-wrap: wrap;
  border: 1px solid #ddd;
  padding: 20px;
  margin: 10px;
  width: 400px;
  transition: background-color 0.3s ease;
}
.productWrap.green {
  background-color: lightgreen;
}
.productWrap.blue {
  background-color: lightblue;
}
.product-image {
  width: 350px;
  height: 350px;
}
.product-image img {
  width: 100%;
  height: 100%;
}
.product-info {
  padding: 10px;
}
.add-to-cart-button {
  background-color: #4caf50;
  height: 45px;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 10px 0;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.add-to-cart-button:hover {
  background-color: #45a049;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
