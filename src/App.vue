<template>
  <div id="app">
    <Banner
      msg="Lorem, ipsum dolor"
      description="Lorem, ipsum dolor sit amet consectetur adipisicing elit. Ducimus"
    />
    <Introduction :data="introductionData" />
    <CardList />
    <FilteredList v-on:changeTitle="updateTitle($event)" />
  </div>
</template>

<script>
import CardList from "./components/CardList.vue";
import FilteredList from "./components/FilteredList.vue";
import Banner from "./components/Banner.vue";
import Introduction from "./components/Introduction.vue";

export default {
  name: "App",
  components: {
    CardList,
    FilteredList,
    Banner,
    Introduction,
  },
  data() {
    return {
      title: "Parent title",
      introductionData: {
        subtitle: "Why Choose Us",
        title: "Welcome to Our Company",
        titleSpan: "collaboration and expertise",
        titleLarge: true,
        nopaddingbottom: false,
        titleWhite: true,
        paddingmediumbottom: true,
        paragraphContent: [
          {
            id: 1,
            paragraph:
              "Bright Code is a specialized web development agency that partners with fellow agencies, businesses, nonprofits, and educational institutions to create the best platform to tell your story. We create digital solutions that empower exceptional results.",
          },
        ],
        btntext: "Get in Touch",
      },
    };
  },
  methods: {
    updateTitle(title) {
      this.title = title;
    },
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
}
</style>
