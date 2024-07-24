<template>
  <section
    class="banner-with-img banner_overlay relative overflow-x-hidden bg-darkBlue flex items-end text-white z-[1] pt-28 pb-24 min-h-[752px] desktop:min-h-[630px] tablet:min-h-[560px] tablet:pt-24 tablet:pb-14 md:min-h-[552px] md:pt-24 md:pb-12 md:items-center"
  ></section>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      // BannerImage: require("../assets/green-socks.png"),
      // bannerLeftImg: require("../assets/green-socks.png"),
      mainTitle: "xyg",
      buttonText: "xyg",
    };
  },
};
</script>

<style scoped>
/* Add your CSS styles here */
</style>
