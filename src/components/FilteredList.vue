<template>
  <div>
    <h1>Item List</h1>
    <div class="filters">
      <label>
        <input type="checkbox" v-model="filters.categoryA" />
        Category A
      </label>
      <label>
        <input type="checkbox" v-model="filters.categoryB" />
        Category B
      </label>
      <label>
        <input type="checkbox" v-model="filters.categoryC" />
        Category C
      </label>
    </div>
    <ul>
      <li v-for="item in filteredItems" :key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { id: 1, name: "Item 1", category: "A" },
        { id: 2, name: "Item 2", category: "B" },
        { id: 3, name: "Item 3", category: "A" },
        { id: 4, name: "Item 4", category: "C" },
        { id: 5, name: "Item 5", category: "B" },
      ],
      filters: {
        categoryA: false,
        categoryB: false,
        categoryC: false,
      },
    };
  },
  computed: {
    filteredItems() {
      return this.items.filter((item) => {
        if (this.filters.categoryA && item.category === "A") return true;
        if (this.filters.categoryB && item.category === "B") return true;
        if (this.filters.categoryC && item.category === "C") return true;
        return false;
      });
    },
  },
};
</script>

<style>
.filters {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
</style>
