<template>
  <div class="w-full">
    <!-- Grid Header -->
    <div class="mb-6">
      <h2 class="text-2xl font-semibold text-breathe-dark-primary">
        {{ title || 'Available Services' }}
      </h2>
      <p v-if="description" class="mt-2 text-breathe-dark-secondary">
        {{ description }}
      </p>
    </div>

    <!-- Filters -->
    <div
      v-if="showFilters"
      class="mb-6 p-4 bg-white border border-breathe-light-secondary rounded-md"
    >
      <div class="flex flex-wrap gap-4">
        <!-- Search Input -->
        <div class="flex-1 min-w-[200px]">
          <input
            v-model="searchQuery"
            type="text"
            placeholder="Search services..."
            class="w-full px-4 py-2 rounded-md border border-breathe-light-secondary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent text-breathe-dark-primary placeholder-breathe-light-primary/60"
          />
        </div>

        <!-- Duration Filter -->
        <div class="w-48">
          <select
            v-model="selectedDuration"
            class="w-full px-4 py-2 rounded-md border border-breathe-light-secondary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent text-breathe-dark-primary bg-white"
          >
            <option value="">All Durations</option>
            <option
              v-for="duration in availableDurations"
              :key="duration"
              :value="duration"
            >
              {{ duration }} minutes
            </option>
          </select>
        </div>

        <!-- Clear Filters Button -->
        <button
          v-if="hasActiveFilters"
          @click="clearFilters"
          class="px-4 py-2 text-breathe-dark-primary hover:bg-breathe-light-secondary/10 rounded-md focus:outline-none focus:ring-2 focus:ring-breathe-light-primary transition-colors duration-200"
        >
          Clear Filters
        </button>
      </div>
    </div>

    <!-- Loading State -->
    <div
      v-if="loading"
      class="w-full h-64 flex items-center justify-center text-breathe-light-primary"
    >
      <svg
        class="animate-spin h-8 w-8"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
      >
        <circle
          class="opacity-25"
          cx="12"
          cy="12"
          r="10"
          stroke="currentColor"
          stroke-width="4"
        ></circle>
        <path
          class="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
        ></path>
      </svg>
    </div>

    <!-- Grid Layout -->
    <div
      v-else
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6"
      :class="{ 'opacity-50': loading }"
    >
      <template v-if="filteredTemplates.length">
        <service-template-card
          v-for="template in filteredTemplates"
          :key="template.id"
          :template="template"
          :disabled="isTemplateDisabled(template)"
          @select="$emit('select', template)"
        />
      </template>
      <div
        v-else
        class="col-span-full p-8 text-center border border-breathe-light-secondary rounded-md bg-white"
      >
        <p class="text-breathe-dark-secondary">
          {{ noResultsMessage || 'No services found matching your criteria.' }}
        </p>
      </div>
    </div>

    <!-- Pagination -->
    <div
      v-if="showPagination && totalPages > 1"
      class="mt-6 flex justify-center space-x-2"
    >
      <button
        v-for="page in totalPages"
        :key="page"
        @click="currentPage = page"
        :class="[
          'px-4 py-2 rounded-md transition-colors duration-200',
          currentPage === page
            ? 'bg-breathe-dark-tertiary text-white'
            : 'text-breathe-dark-primary hover:bg-breathe-light-secondary/10'
        ]"
        :aria-current="currentPage === page ? 'page' : undefined"
      >
        {{ page }}
      </button>
    </div>
  </div>
</template>

<script>
import ServiceTemplateCard from './ServiceTemplateCard.vue'

export default {
  name: 'ServiceTemplatesGrid',
  components: {
    ServiceTemplateCard
  },
  props: {
    templates: {
      type: Array,
      required: true,
      default: () => []
    },
    title: {
      type: String,
      default: ''
    },
    description: {
      type: String,
      default: ''
    },
    loading: {
      type: Boolean,
      default: false
    },
    showFilters: {
      type: Boolean,
      default: true
    },
    showPagination: {
      type: Boolean,
      default: true
    },
    itemsPerPage: {
      type: Number,
      default: 9
    },
    noResultsMessage: {
      type: String,
      default: ''
    },
    disabledTemplates: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      searchQuery: '',
      selectedDuration: '',
      currentPage: 1
    }
  },
  computed: {
    availableDurations() {
      return [...new Set(this.templates.map(t => t.duration))].sort((a, b) => a - b)
    },
    hasActiveFilters() {
      return this.searchQuery || this.selectedDuration
    },
    filteredTemplates() {
      let filtered = [...this.templates]

      // Apply search filter
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase()
        filtered = filtered.filter(template =>
          template.name.toLowerCase().includes(query) ||
          template.description.toLowerCase().includes(query) ||
          template.tags.some(tag => tag.toLowerCase().includes(query))
        )
      }

      // Apply duration filter
      if (this.selectedDuration) {
        filtered = filtered.filter(
          template => template.duration === parseInt(this.selectedDuration)
        )
      }

      // Apply pagination
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return filtered.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.templates.length / this.itemsPerPage)
    }
  },
  methods: {
    clearFilters() {
      this.searchQuery = ''
      this.selectedDuration = ''
      this.currentPage = 1
    },
    isTemplateDisabled(template) {
      return this.disabledTemplates.includes(template.id)
    }
  },
  watch: {
    searchQuery() {
      this.currentPage = 1
    },
    selectedDuration() {
      this.currentPage = 1
    }
  }
}
</script>

<style scoped>
.service-templates-grid {
  /* Secondary Light for borders */
  --grid-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --grid-text: var(--breathe-dark-primary);
  /* Primary Light for focus rings */
  --grid-focus: var(--breathe-light-primary);
  /* Tertiary Dark for pagination */
  --grid-pagination: var(--breathe-dark-tertiary);
}
</style>
