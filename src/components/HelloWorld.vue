<template>
  <div class="hello" id="card">
    <h1>{{ product }}</h1>
    <a :href="href">{{linkData}}</a>
    <img :src="image" alt="Vue logo">
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data(){
    return{
      product: "Abc",
      linkData: "Anchor",
      href: "/",
      image: "../assets/green-socks.png",
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
h1 {
  margin: 40px 0 0;
  color: #42b983;
}
</style>
