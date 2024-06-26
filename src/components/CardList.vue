<template>
  <div class="main">
    <!-- Search Bar -->
    <div class="container">
      <div class="searchAndDropdown">
        <div class="dropDownWrap">
          <!-- Data Category Dropdown -->
          <div class="dropdown">
            <button @click="toggleDropdown('data')" class="dropdown-button">
              Data
              <span
                :class="{
                  'arrow-up': showDropdown.data,
                  'arrow-down': !showDropdown.data,
                }"
              ></span>
            </button>
            <div v-if="showDropdown.data" class="dropdown-content">
              <label
                v-for="(count, date) in uniqueStartDatesWithCount"
                :key="date"
                class="dropdown-item"
              >
                <input type="checkbox" v-model="filters.data" :value="date" />
                {{ date }} {{ count > 1 ? count : "" }}
              </label>
            </div>
          </div>

          <!-- Event Category Dropdown -->
          <div class="dropdown">
            <button @click="toggleDropdown('event')" class="dropdown-button">
              Event
              <span
                :class="{
                  'arrow-up': showDropdown.event,
                  'arrow-down': !showDropdown.event,
                }"
              ></span>
            </button>
            <div v-if="showDropdown.event" class="dropdown-content">
              <div
                v-for="(count, type) in uniqueEventTypesWithCount"
                :key="type"
              >
                <label
                  v-for="individualType in [...new Set(type.split(','))]"
                  :key="individualType"
                  class="dropdown-item"
                >
                  <input
                    type="checkbox"
                    v-model="filters.event"
                    :value="individualType.trim()"
                  />
                  {{ individualType.trim() }} ({{ count }})
                </label>
              </div>
            </div>
          </div>

          <!-- Audience Category Dropdown -->
          <div class="dropdown">
            <button @click="toggleDropdown('audience')" class="dropdown-button">
              Audience
              <span
                :class="{
                  'arrow-up': showDropdown.audience,
                  'arrow-down': !showDropdown.audience,
                }"
              ></span>
            </button>
            <div v-if="showDropdown.audience" class="dropdown-content">
              <div
                v-for="(count, audience) in uniqueAudiencesSetWithCount"
                :key="audience"
              >
                <template v-if="audience.trim()">
                  <label
                    v-for="individualAudience in audience
                      .split(',')
                      .map((a) =>
                        a.trim().replace(/&amp;/g, ' ').replace(/amp;/g, ' ')
                      )
                      .filter((a) => a)"
                    :key="individualAudience"
                    class="dropdown-item"
                  >
                    <input
                      type="checkbox"
                      v-model="filters.audience"
                      :value="individualAudience"
                    />
                    {{ individualAudience }} ({{ count }})
                  </label>
                </template>
              </div>
            </div>
          </div>
        </div>

        <div class="totalCards">
          <p>Total Cards: {{ totalCards }}</p>
        </div>
        <div class="searchWrap">
          <input
            type="text"
            v-model="searchQuery"
            placeholder="Search cards..."
            class="search-bar"
          />
        </div>
      </div>
      <div class="">
        <div class="selectedFilters">
          <span
            v-for="(filter, index) in selectedFilters"
            :key="index"
            class="selectedFilter"
          >
            {{ filter.category }}: {{ filter.value }}
            <span
              class="remove-filter-icon"
              @click="removeSelectedFilter(filter)"
            >
              ✕
            </span>
          </span>
        </div>
        <button @click="clearFilters">Clear Filters</button>
      </div>

      <div class="">
        <div v-if="paginatedCards.length > 0" class="cards">
          <div v-for="card in paginatedCards" :key="card.id" class="card">
            <h2>{{ card.title }}</h2>
            <p>Event Type: {{ card.field_event_type }}</p>
            <p>Audience: {{ card.field_audience }}</p>
            <p>Location: {{ card.field_designation }}</p>
            <p>Start Date: {{ card.field_event_start_date }}</p>
            <p>End Date: {{ card.field_event_end_date }}</p>
            <a :href="card.field_id" target="_blank">More Info</a>
          </div>
        </div>
        <div v-else>
          <h2>No cards available.</h2>
        </div>
      </div>
      <!-- Pagination Controls -->
      <div v-if="shouldShowPagination" class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">
          &#8592; Previous
        </button>
        <span>Page {{ currentPage }} of {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">
          Next &#8594;
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import eventsData from "./events.json";

export default {
  data() {
    return {
      searchQuery: "",
      filters: {
        data: [],
        event: [],
        audience: [],
      },
      showDropdown: {
        data: false,
        event: false,
        audience: false,
      },
      cards: [],
      currentPage: 1,
      perPage: 9,
    };
  },
  computed: {
    // Compute counts for unique data categories with their respective counts
    uniqueStartDatesWithCount() {
      const counts = {};
      const cardsToCount = this.filteredCards;

      cardsToCount.forEach((card) => {
        if (card.field_event_start_date) {
          counts[card.field_event_start_date] =
            (counts[card.field_event_start_date] || 0) + 1;
        }
      });

      return counts;
    },

    uniqueEventTypesWithCount() {
      const counts = {};
      const filteredData = this.filteredCards;

      filteredData.forEach((card) => {
        if (card.field_event_type) {
          if (counts[card.field_event_type]) {
            counts[card.field_event_type]++;
          } else {
            counts[card.field_event_type] = 1;
          }
        }
      });

      return counts;
    },

    uniqueAudiencesSetWithCount() {
      const counts = {};
      const filteredData = this.filteredCards;

      filteredData.forEach((card) => {
        if (card.field_audience) {
          card.field_audience
            .split(",")
            .map((audience) => audience.trim())
            .forEach((audienceItem) => {
              if (counts[audienceItem]) {
                counts[audienceItem]++;
              } else {
                counts[audienceItem] = 1;
              }
            });
        }
      });

      return counts;
    },

    // Filtered cards based on search query and selected filters
    filteredCards() {
      console.log(this);
      return this.cards.filter((card) => {
        // Filter by search query
        if (
          this.searchQuery &&
          !card.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        ) {
          return false;
        }

        // Filter by data category
        if (
          this.filters.data.length > 0 &&
          !this.filters.data.includes(card.field_event_start_date)
        ) {
          return false;
        }

        // Filter by event category
        if (
          this.filters.event.length > 0 &&
          !this.filters.event.includes(card.field_event_type)
        ) {
          return false;
        }

        // Filter by audience category
        if (this.filters.audience.length > 0) {
          const audienceValues = card.field_audience
            .split(",")
            .map((value) => value.trim());
          if (
            !this.filters.audience.some((aud) => audienceValues.includes(aud))
          ) {
            return false;
          }
        }

        return true;
      });
    },

    // Paginated cards based on current page and per page count
    paginatedCards() {
      const startIndex = (this.currentPage - 1) * this.perPage;
      const uniqueCards = [];

      const seen = new Set();
      for (let i = 0; i < this.filteredCards.length; i++) {
        const card = this.filteredCards[i];
        if (!seen.has(card.id)) {
          seen.add(card.id);
          uniqueCards.push(card);
        }
      }

      return uniqueCards.slice(startIndex, startIndex + this.perPage);
    },

    // Total number of filtered cards
    totalCards() {
      return this.filteredCards.length;
    },

    // Total number of pages based on filtered cards and per page count
    totalPages() {
      return Math.ceil(this.filteredCards.length / this.perPage);
    },

    // Whether pagination should be displayed
    shouldShowPagination() {
      return this.totalCards > this.perPage;
    },

    // Selected filters to display
    selectedFilters() {
      const selected = [];

      if (this.filters.data.length > 0) {
        selected.push(
          ...this.filters.data.map((date) => ({
            category: "data",
            value: date,
          }))
        );
      }

      if (this.filters.event.length > 0) {
        selected.push(
          ...this.filters.event.map((type) => ({
            category: "event",
            value: type,
          }))
        );
      }

      if (this.filters.audience.length > 0) {
        selected.push(
          ...this.filters.audience.map((audience) => ({
            category: "audience",
            value: audience,
          }))
        );
      }

      return selected;
    },
  },

  mounted() {
    // Set initial data from eventsData
    this.cards = eventsData.cards;

    // Add event listener to handle clicks outside dropdowns
    document.addEventListener("click", this.handleDocumentClick);
  },
  beforeUnmount() {
    // Remove event listener when component is unmounted
    document.removeEventListener("click", this.handleDocumentClick);
  },

  methods: {
    // Update filtered data when filteredCards change
    updateFilteredData() {
      this.filterdCardsData.data = this.filteredCards.filter(
        (card) => card.field_event_start_date
      );
      this.filterdCardsData.event = this.filteredCards.filter(
        (card) => card.field_event_type
      );
      this.filterdCardsData.audience = this.filteredCards.filter(
        (card) => card.field_audience
      );
    },

    // Toggle dropdown visibility
    toggleDropdown(category) {
      for (let key in this.showDropdown) {
        if (key !== category) {
          this.showDropdown[key] = false;
        }
      }
      this.showDropdown[category] = !this.showDropdown[category];
    },

    // Handle clicks outside dropdowns to close them
    handleDocumentClick(event) {
      let isDropdownClick = false;
      const dropdownButtons = document.querySelectorAll(".dropdown-button");
      dropdownButtons.forEach((button) => {
        if (button.contains(event.target)) {
          isDropdownClick = true;
        }
      });

      const dropdownContents = document.querySelectorAll(".dropdown-content");
      dropdownContents.forEach((content) => {
        if (content.contains(event.target)) {
          isDropdownClick = true;
        }
      });

      if (!isDropdownClick) {
        for (let key in this.showDropdown) {
          this.showDropdown[key] = false;
        }
      }
    },

    // Remove selected filter
    removeSelectedFilter(filter) {
      const index = this.selectedFilters.findIndex(
        (selected) =>
          selected.category === filter.category &&
          selected.value === filter.value
      );
      if (index !== -1) {
        this.selectedFilters.splice(index, 1);
      }
      const filterIndex = this.filters[filter.category].indexOf(filter.value);
      if (filterIndex !== -1) {
        this.filters[filter.category].splice(filterIndex, 1);
      }
    },

    // Clear all filters and search query
    clearFilters() {
      this.filters.data = [];
      this.filters.event = [];
      this.filters.audience = [];
      this.searchQuery = "";
    },

    // Navigate to next page
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },

    // Navigate to previous page
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
  },
};
</script>


<style>
.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 20px;
}

.pagination button {
  background-color: #4185b5;
  color: white;
  border: none;
  padding: 10px 15px;
  margin: 0 5px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.pagination button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.pagination span {
  margin: 0 10px;
  font-size: 16px;
}
.selectedFilters {
  display: flex;
  flex-wrap: wrap;
  margin-top: 10px;
}

.selectedFilter {
  background-color: #f0f0f0;
  padding: 5px;
  border-radius: 4px;
  margin-right: 5px;
  margin-bottom: 5px;
  display: flex;
  align-items: center;
}

.remove-filter-icon {
  cursor: pointer;
  margin-left: 5px;
}

.cards {
  display: flex;
  flex-wrap: wrap;
  margin-top: 20px;
  margin-left: -15px;
  width: calc(100% + 30px);
  overflow: hidden;
  min-height: 300px;
}
.main {
  overflow: hidden;
  box-sizing: border-box;
}
.card {
  border: 1px solid #ccc;
  padding: 20px;
  border-radius: 8px;
  width: calc(33.33% - 30px);
  margin: 0 15px 20px;
  box-sizing: border-box;
  background: #29325b0f;
}

.dropdown {
  position: relative;
  display: inline-block;
  margin-right: 10px;
}
.dropdown button {
  font-size: 16px;
}
h2 {
  color: #4185b5;
}
.dropdown-button {
  padding: 10px;
  border: none;
  background-color: #3498db;
  color: white;
  cursor: pointer;
  border-radius: 4px;
}
.dropdown-item {
  text-align: left;
}
a {
  color: black;
  transition: color 0.3s ease-in-out;
}
a:hover {
  color: #3498db;
  transition: color 0.3s ease-in-out;
}
.container {
  width: 100%;
  max-width: 1400px;
  padding: 0 20px;
  margin: 0 auto;
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
  max-height: 200px;
  overflow-y: auto;
}
.totalCards p {
  color: #fff;
}

.dropdown-item {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
  padding: 5px 0;
}
.searchAndDropdown {
  display: flex;
  background-color: #3498db;
  align-items: center;
  padding: 20px 50px;
  justify-content: space-between;
}
/* .dropDownWrap {
} */
.dropdown-item input {
  margin-right: 10px;
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
  width: 100%;
  min-width: 350px;
  box-sizing: border-box;
  border-radius: 4px;
  border: 1px solid #ccc;
}
</style>
