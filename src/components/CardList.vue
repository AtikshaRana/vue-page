<template>
  <div>
    <!-- Search Bar -->
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
        <label
          v-for="date in [...new Set(uniqueStartDates)]"
          :key="date"
          class="dropdown-item"
        >
          <input type="checkbox" v-model="filters.data" :value="date" />
          {{ date }}
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
        <div v-for="type in uniqueEventTypes" :key="type">
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
            {{ individualType.trim() }}
          </label>
        </div>
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
        <div v-for="audience in uniqueAudiencesSet" :key="audience">
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
              {{ individualAudience }}
            </label>
          </template>
        </div>
      </div>
    </div>

    <!-- Total Cards Display -->
    <p>Total Cards: {{ totalCards }}</p>

    <div class="cards">
      <div v-for="card in filteredCards" :key="card.title" class="card">
        <h2>{{ card.title }}</h2>
        <p>Event Type: {{ card.field_event_type }}</p>
        <p>Audience: {{ card.field_audience }}</p>
        <p>Location: {{ card.field_designation }}</p>
        <p>Start Date: {{ card.field_event_start_date }}</p>
        <p>End Date: {{ card.field_event_end_date }}</p>
        <a :href="card.field_id" target="_blank">More Info</a>
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
      cards: eventsData.cards,
    };
  },

  computed: {
    uniqueAudiencesSet() {
      const deduplicatedAudiences = new Set();
      this.uniqueAudiences.forEach((audience) => {
        audience
          .split(",")
          .map((a) => a.trim().replace(/&amp;/g, " ").replace(/amp;/g, " "))
          .filter((a) => a)
          .forEach((audienceItem) => deduplicatedAudiences.add(audienceItem));
      });
      return [...deduplicatedAudiences];
    },
    uniqueStartDates() {
      return [
        ...new Set(this.cards.map((card) => card.field_event_start_date)),
      ];
    },
    uniqueEventTypes() {
      return [...new Set(this.cards.map((card) => card.field_event_type))];
    },
    uniqueAudiences() {
      return [...new Set(this.cards.map((card) => card.field_audience))];
    },

    filteredCards() {
      return this.cards.filter((card) => {
        if (
          this.searchQuery &&
          !card.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        ) {
          return false;
        }

        const { data, event, audience } = this.filters;
        if (
          (data.length && !data.includes(card.field_event_start_date)) ||
          (event.length && !event.includes(card.field_event_type)) ||
          (audience.length && !audience.includes(card.field_audience))
        ) {
          return false;
        }

        return true;
      });
    },

    totalCards() {
      return this.filteredCards.length;
    },
  },

  methods: {
    toggleDropdown(category) {
      for (let key in this.showDropdown) {
        if (key !== category) {
          this.showDropdown[key] = false;
        }
      }
      this.showDropdown[category] = !this.showDropdown[category];
    },
    handleDocumentClick(event) {
      const dropdownContainer = this.$refs.dropdownContainer;
      if (!dropdownContainer.contains(event.target)) {
        for (let key in this.showDropdown) {
          this.showDropdown[key] = false;
        }
      }
    },
    getUniqueAudienceValues(audience) {
      // Ensure audience is a string and split, trim, replace, filter, and return unique values
      if (typeof audience !== "string") {
        return [];
      }
      return [
        ...new Set(
          audience
            .split(",")
            .map((a) => a.trim().replace(/&amp;/g, " ").replace(/amp;/g, " "))
            .filter((a) => a)
        ),
      ];
    },
  },
};
</script>

<style>
/* Your existing styles */
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
.dropdown-item {
  text-align: left;
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

.dropdown-item {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
  padding: 5px 0;
}

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
  margin-bottom: 20px;
  width: 100%;
  box-sizing: border-box;
  border-radius: 4px;
  border: 1px solid #ccc;
}
</style>
