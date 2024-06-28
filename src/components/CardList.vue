<template>
  <div>
    <h1>Item List</h1>
    <input
      type="text"
      v-model="searchQuery"
      placeholder="Search cards..."
      class="search-bar"
    />

    <!-- Data Category Dropdown -->
    <div class="dropdown">
      <button @click="toggleDropdown('data')" class="dropdown-button">
        Data Category
        <span
          :class="{
            'arrow-up': showDropdown.data,
            'arrow-down': !showDropdown.data,
          }"
        ></span>
      </button>
      <div v-if="showDropdown.data" class="dropdown-content">
        <label>
          <input type="checkbox" v-model="filters.data.categoryA" />
          Data Category A
        </label>
        <label>
          <input type="checkbox" v-model="filters.data.categoryB" />
          Data Category B
        </label>
      </div>
    </div>

    <!-- Event Category Dropdown -->
    <div class="dropdown">
      <button @click="toggleDropdown('event')" class="dropdown-button">
        Event Category
        <span
          :class="{
            'arrow-up': showDropdown.event,
            'arrow-down': !showDropdown.event,
          }"
        ></span>
      </button>
      <div v-if="showDropdown.event" class="dropdown-content">
        <label>
          <input type="checkbox" v-model="filters.event.categoryA" />
          Event Category A
        </label>
        <label>
          <input type="checkbox" v-model="filters.event.categoryB" />
          Event Category B
        </label>
      </div>
    </div>

    <!-- Audience Category Dropdown -->
    <div class="dropdown">
      <button @click="toggleDropdown('audience')" class="dropdown-button">
        Audience Category
        <span
          :class="{
            'arrow-up': showDropdown.audience,
            'arrow-down': !showDropdown.audience,
          }"
        ></span>
      </button>
      <div v-if="showDropdown.audience" class="dropdown-content">
        <label>
          <input type="checkbox" v-model="filters.audience.categoryA" />
          Audience Category A
        </label>
        <label>
          <input type="checkbox" v-model="filters.audience.categoryB" />
          Audience Category B
        </label>
      </div>
    </div>

    <p>Total Cards: {{ totalCards }}</p>
    <div class="cards">
      <div v-for="card in filteredCards" :key="card.id" class="card">
        <h2>{{ card.name }}</h2>
        <p>Category: {{ card.category }}</p>
        <p>Type: {{ card.type }}</p>
        <p>Audience: {{ card.audience }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: "",
      cards: [
        {
          id: 1,
          name: "Card 1",
          category: "A",
          type: "Data",
          audience: "Audience A",
        },
        {
          id: 2,
          name: "Card 2",
          category: "B",
          type: "Event",
          audience: "Audience B",
        },
        {
          id: 3,
          name: "Card 3",
          category: "A",
          type: "Data",
          audience: "Audience A",
        },
        {
          id: 4,
          name: "Card 4",
          category: "C",
          type: "Event",
          audience: "Audience C",
        },
        {
          id: 5,
          name: "Card 5",
          category: "B",
          type: "Audience",
          audience: "Audience B",
        },
      ],
      filters: {
        data: {
          categoryA: false,
          categoryB: false,
        },
        event: {
          categoryA: false,
          categoryB: false,
        },
        audience: {
          categoryA: false,
          categoryB: false,
        },
      },
      showDropdown: {
        data: false,
        event: false,
        audience: false,
      },
    };
  },
  computed: {
    filteredCards() {
      return this.cards.filter((card) => {
        // Search functionality
        if (
          this.searchQuery &&
          !card.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        ) {
          return false;
        }

        // Filtering functionality
        const filters = this.filters;
        if (
          filters.data.categoryA &&
          card.type === "Data" &&
          card.category === "A"
        )
          return true;
        if (
          filters.data.categoryB &&
          card.type === "Data" &&
          card.category === "B"
        )
          return true;
        if (
          filters.event.categoryA &&
          card.type === "Event" &&
          card.category === "A"
        )
          return true;
        if (
          filters.event.categoryB &&
          card.type === "Event" &&
          card.category === "B"
        )
          return true;
        if (filters.audience.categoryA && card.audience === "Audience A")
          return true;
        if (filters.audience.categoryB && card.audience === "Audience B")
          return true;

        // If no filters are selected, show all cards
        const noFiltersSelected =
          Object.values(filters.data).every((value) => !value) &&
          Object.values(filters.event).every((value) => !value) &&
          Object.values(filters.audience).every((value) => !value);
        if (noFiltersSelected) return true;

        return false;
      });
    },
    totalCards() {
      return this.filteredCards.length;
    },
  },
  methods: {
    toggleDropdown(category) {
      // Close other dropdowns
      for (let key in this.showDropdown) {
        if (key !== category) {
          this.showDropdown[key] = false;
        }
      }
      // Toggle the selected dropdown
      this.showDropdown[category] = !this.showDropdown[category];
    },
  },
};
</script>

<style>
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-top: 20px;
}

.card {
  border: 1px solid #ccc;
  padding: 20px;
  border-radius: 8px;
  width: calc(33% - 40px);
  box-sizing: border-box;
}

.dropdown {
  position: relative;
  display: inline-block;
  margin-right: 10px;
}

.dropdown-button {
  padding: 10px;
  border: none;
  background-color: #3498db;
  color: white;
  cursor: pointer;
  border-radius: 4px;
}

.dropdown-content {
  display: flex;
  flex-direction: column;
  position: absolute;
  background-color: white;
  box-shadow: 0px 8px 16px rgba(0, 0, 0, 0.2);
  padding: 10px;
  border-radius: 4px;
  margin-top: 5px;
  z-index: 1;
  width: 200px;
}

.arrow-up::before {
  content: "▲";
  margin-left: 10px;
}

.arrow-down::before {
  content: "▼";
  margin-left: 10px;
}

label {
  margin-bottom: 5px;
}

.search-bar {
  padding: 10px;
  margin-bottom: 20px;
  width: 100%;
  box-sizing: border-box;
  border-radius: 4px;
  border: 1px solid #ccc;
}
</style>
