<template>
  <div class="cardsWrap">
    <div class="productWrap">
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
        <div v-for="variant in variants" :key="variant.variantId">
          <p>{{ variant.variantColor }}</p>
        </div>
      </div>
      <button v-on:click="addToCart">Add to cart</button>
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
        },
        {
          variantColor: "Blue",
          variantId: "2",
        },
      ],
      cart: 0,
    };
  },
  methods: {
    addToCart() {
      this.cart += 1;
    },
  },
};
</script>
<style>
.productWrap {
  display: flex;
  flex: wrap;
}
.product-image {
  width: 350px;
  height: 350px;
}
.product-image img {
  width: 350px;
  height: 350px;
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
