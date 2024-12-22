<template>
  <div class="w-full">
    <!-- Header -->
    <div class="mb-4">
      <h3 class="text-lg font-semibold text-breathe-dark-primary">
        {{ label || 'Select Your Timezone' }}
      </h3>
      <p v-if="description" class="mt-1 text-sm text-breathe-dark-secondary">
        {{ description }}
      </p>
    </div>

    <!-- Timezone Selector -->
    <div class="relative">
      <!-- Search Input -->
      <div class="mb-3">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Search for your timezone..."
          class="w-full px-4 py-2 border border-breathe-light-secondary rounded-lg text-breathe-dark-primary placeholder-breathe-light-primary/60 focus:outline-none focus:ring-2 focus:ring-breathe-light-primary"
          :aria-label="'Search for timezone'"
        />
      </div>

      <!-- Timezone List -->
      <div class="border border-breathe-light-secondary rounded-lg bg-white overflow-hidden">
        <div class="max-h-60 overflow-y-auto">
          <button
            v-for="timezone in filteredTimezones"
            :key="timezone.name"
            @click="selectTimezone(timezone)"
            class="w-full px-4 py-3 text-left hover:bg-breathe-light-secondary/10 focus:outline-none focus:bg-breathe-light-secondary/10"
            :class="{
              'bg-breathe-dark-tertiary text-white': isSelected(timezone),
              'text-breathe-dark-primary': !isSelected(timezone)
            }"
            :aria-selected="isSelected(timezone)"
          >
            <div class="flex justify-between items-center">
              <span class="font-medium">{{ timezone.name }}</span>
              <span class="text-sm" :class="{
                'text-white': isSelected(timezone),
                'text-breathe-dark-secondary': !isSelected(timezone)
              }">
                {{ formatOffset(timezone.offset) }}
              </span>
            </div>
            <div class="text-sm mt-1" :class="{
              'text-white/90': isSelected(timezone),
              'text-breathe-dark-secondary': !isSelected(timezone)
            }">
              {{ timezone.city }}
            </div>
          </button>

          <!-- Empty State -->
          <div
            v-if="filteredTimezones.length === 0"
            class="px-4 py-3 text-breathe-dark-secondary text-center"
          >
            No timezones found matching your search.
          </div>
        </div>
      </div>

      <!-- Selected Timezone Display -->
      <div v-if="selectedTimezone" class="mt-4 pt-4 border-t border-breathe-light-secondary">
        <p class="text-sm text-breathe-dark-secondary">
          Selected Timezone:
          <span class="font-medium text-breathe-dark-primary">
            {{ selectedTimezone.name }} ({{ formatOffset(selectedTimezone.offset) }})
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SelectATimezone',
  props: {
    label: {
      type: String,
      default: ''
    },
    description: {
      type: String,
      default: ''
    },
    value: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      searchQuery: '',
      selectedTimezone: this.value,
      timezones: [
        {
          name: 'Pacific Time',
          city: 'Los Angeles, Vancouver',
          offset: -8
        },
        {
          name: 'Mountain Time',
          city: 'Denver, Phoenix',
          offset: -7
        },
        {
          name: 'Central Time',
          city: 'Chicago, Mexico City',
          offset: -6
        },
        {
          name: 'Eastern Time',
          city: 'New York, Toronto',
          offset: -5
        },
        {
          name: 'Atlantic Time',
          city: 'Halifax, San Juan',
          offset: -4
        },
        {
          name: 'GMT',
          city: 'London, Dublin',
          offset: 0
        },
        {
          name: 'Central European Time',
          city: 'Paris, Berlin',
          offset: 1
        },
        {
          name: 'Eastern European Time',
          city: 'Helsinki, Cairo',
          offset: 2
        },
        {
          name: 'India Standard Time',
          city: 'Mumbai, New Delhi',
          offset: 5.5
        },
        {
          name: 'China Standard Time',
          city: 'Beijing, Shanghai',
          offset: 8
        },
        {
          name: 'Japan Standard Time',
          city: 'Tokyo, Seoul',
          offset: 9
        },
        {
          name: 'Australian Eastern Time',
          city: 'Sydney, Melbourne',
          offset: 10
        },
        {
          name: 'New Zealand Standard Time',
          city: 'Auckland, Wellington',
          offset: 12
        }
      ]
    }
  },
  computed: {
    filteredTimezones() {
      if (!this.searchQuery) return this.timezones

      const query = this.searchQuery.toLowerCase()
      return this.timezones.filter(timezone =>
        timezone.name.toLowerCase().includes(query) ||
        timezone.city.toLowerCase().includes(query)
      )
    }
  },
  methods: {
    selectTimezone(timezone) {
      this.selectedTimezone = timezone
      this.$emit('input', timezone)
    },
    isSelected(timezone) {
      return (
        this.selectedTimezone &&
        this.selectedTimezone.name === timezone.name
      )
    },
    formatOffset(offset) {
      const sign = offset >= 0 ? '+' : ''
      const hours = Math.floor(Math.abs(offset))
      const minutes = Math.abs((offset % 1) * 60)
      return `UTC${sign}${offset}${minutes ? `:${minutes}` : ''}`
    }
  },
  watch: {
    value(newValue) {
      this.selectedTimezone = newValue
    }
  }
}
</script>

<style scoped>
.select-a-timezone {
  /* Secondary Light for borders */
  --timezone-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --timezone-text: var(--breathe-dark-primary);
  /* Primary Light for focus rings */
  --timezone-focus: var(--breathe-light-primary);
  /* Tertiary Dark for selected timezone */
  --timezone-selected: var(--breathe-dark-tertiary);
}
</style>
