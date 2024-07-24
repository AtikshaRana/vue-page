<template>
  <section
    class="banner-with-img mb-[60px] banner_overlay relative overflow-x-hidden bg-darkBlue flex items-end text-white z-[1] pt-28 pb-24 min-h-[752px] desktop:min-h-[630px] tablet:min-h-[560px] tablet:pt-24 tablet:pb-14 md:min-h-[552px] md:pt-24 md:pb-12 md:items-center"
  >
    <div
      v-if="BannerImage"
      class="bg_image absolute top-0 left-0 w-full h-full"
    >
      <img
        :src="BannerImage"
        alt="bg-img"
        class="w-full h-full object-cover object-center"
      />
    </div>

    <div class="container">
      <div
        v-if="bannerLeftImg"
        class="left_img absolute bottom-0 right-[60%] w-full max-w-[526px] h-[506px] z-[1] desktop:max-w-[450px] desktop:h-[450px] tablet:max-w-[400px] tablet:h-[400px] xl:right-auto tablet:left-0 md:h-[310px] md:max-w-[325px] md:left-[50%] md:translate-x-[-50%]"
      >
        <img
          :src="bannerLeftImg"
          alt="left-img"
          class="w-full h-full object-cover"
        />
      </div>
      <div
        class="w-full max-w-[750px] ml-auto relative z-[2] text-left xl:max-w-full desktop:w-[calc(100%-450px)] tablet:w-[calc(100%-400px)] md:text-center md:mx-auto"
      >
        <h1 v-if="msg" class="text-white">
          {{ msg }}
        </h1>
        <p>{{ description }}</p>
        <a
          v-if="buttonText"
          :href="buttonText"
          class="mt-14 gradient-btn mx-auto md:mt-6"
        >
          <span>{{ buttonText }}</span>
        </a>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    msg: String,
    description: String,
    data: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      BannerImage: require("@/assets/banner-bg-img.svg"),
      bannerLeftImg: require("@/assets/banner-left-img.png"),
      mainTitle: "xyg",
      buttonText: "Lorem",
    };
  },
};
</script>

<style scoped>
/* Add your CSS styles here */
</style>
