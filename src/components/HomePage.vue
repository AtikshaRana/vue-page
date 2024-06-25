<template>
  <div class="slider-container" ref="slider">
    <div
      class="slider-content"
      @mousedown="startDragging"
      @mousemove="onDrag"
      @mouseup="stopDragging"
      @mouseleave="stopDragging"
    >
      <div class="card" v-for="(card, index) in cards" :key="index">
        <h3>{{ card.title }}</h3>
        <p>{{ card.content }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DraggableSlider",
  data() {
    return {
      isDragging: false,
      startX: 0,
      scrollLeft: 0,
      cards: [
        { title: "Card 1", content: "This is card 1 content" },
        { title: "Card 2", content: "This is card 2 content" },
        { title: "Card 3", content: "This is card 3 content" },
        { title: "Card 1", content: "This is card 1 content" },
        { title: "Card 2", content: "This is card 2 content" },
        { title: "Card 3", content: "This is card 3 content" },
        { title: "Card 1", content: "This is card 1 content" },
        { title: "Card 2", content: "This is card 2 content" },
        { title: "Card 3", content: "This is card 3 content" },
        { title: "Card 1", content: "This is card 1 content" },
        { title: "Card 2", content: "This is card 2 content" },
        // Add more cards as needed
      ],
    };
  },
  methods: {
    startDragging(event) {
      this.isDragging = true;
      this.startX = event.pageX - this.$refs.slider.scrollLeft;
      this.scrollLeft = this.$refs.slider.scrollLeft;
    },
    onDrag(event) {
      if (!this.isDragging) return;
      event.preventDefault();
      const x = event.pageX - this.$refs.slider.offsetLeft;
      const walk = (x - this.startX) * 2; // Scroll-fast multiplier
      this.$refs.slider.scrollLeft = this.scrollLeft - walk;
    },
    stopDragging() {
      this.isDragging = false;
    },
  },
};
</script>

<style scoped>
.slider-container {
  overflow: hidden;
  width: 100%;
  cursor: grab;
}

.slider-container:active {
  cursor: grabbing;
}

.slider-content {
  display: flex;
  width: max-content;
  transition: all 0.3s ease;
}

.card {
  min-width: 300px;
  margin: 10px;
  padding: 20px;
  background-color: #fff;
  border: 1px solid #ddd;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}
</style>

