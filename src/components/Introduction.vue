<template>
  <section
    :class="[
      data.nopaddingbottom ? 'no-padding-bottom' : '',
      data.paddingmediumbottom ? 'padding-medium-bottom' : '',
      'intro padding-large-top padding-medium-bottom text-center overflow-x-hidden mb-[50px] ',
    ]"
    id="why-choose-us"
  >
    <div class="container">
      <div class="w-full mx-auto">
        <h6
          v-if="data.subtitle"
          class="text-[#8000FF] uppercase font-normal mb-3 md:mb-1"
        >
          {{ data.subtitle }}
        </h6>

        <h2
          v-if="data.titleSpan"
          :class="[
            data.titleLarge ? 'large' : '',
            data.titleWhite ? 'text-white' : '',
            data.paragraphContent.length ? 'mb-0' : 'mb-[38px] md:mb-[20px]',
            'font-medium text-black',
          ]"
        >
          <span
            class="text_gradient block text-[50px] leading-[65px] desktop:text-[36px] desktop:leading-[60px] tablet:text-[33px] tablet:leading-[55px] md:text-[30px] md:leading-[50px]"
          >
            {{ data.titleSpan }}
          </span>
        </h2>
        <p
          v-else
          :class="[
            data.titleLarge ? 'large' : '',
            data.titleWhite ? 'text-white' : '',
            data.paragraphContent.length ? 'mb-0' : 'mb-[38px] md:mb-[20px]',
            'font-medium text-black',
          ]"
        >
          {{ data.title }}
        </p>

        <div v-for="paragraph in data.paragraphContent" :key="paragraph.id">
          <h5
            class="text-black w-full max-w-[960px] mx-auto mt-5"
            v-html="paragraph.paragraph"
          ></h5>
        </div>

        <div
          v-if="data.btntext"
          class="cursor-pointer mt-7 gradient-btn mx-auto"
          @click="handleScroll"
        >
          <span>{{ data.btntext }}</span>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      required: true,
      default: () => ({
        subtitle: "",
        title: "",
        titleSpan: "",
        titleLarge: false,
        nopaddingbottom: false,
        titleWhite: false,
        paddingmediumbottom: false,
        paragraphContent: [],
        btntext: "",
      }),
    },
  },
  methods: {
    handleScroll() {
      const targetElement = document.getElementById("get-in-touch");
      if (targetElement) {
        targetElement.scrollIntoView({ behavior: "smooth" });
        window.history.replaceState(
          null,
          document.title,
          window.location.pathname
        );
      }
    },
  },
};
</script>

<style scoped>
/* Add your styles here */
</style>
