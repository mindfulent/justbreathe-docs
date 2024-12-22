<template>
  <div class="w-full">
    <!-- Header -->
    <div class="mb-4">
      <h3 class="text-lg font-semibold text-breathe-dark-primary">
        {{ label || 'Select a Date' }}
      </h3>
      <p v-if="description" class="mt-1 text-sm text-breathe-dark-secondary">
        {{ description }}
      </p>
    </div>

    <!-- Calendar Container -->
    <div class="border border-breathe-light-secondary rounded-lg bg-white p-4">
      <!-- Month Navigation -->
      <div class="flex items-center justify-between mb-4">
        <button
          @click="previousMonth"
          class="p-2 hover:bg-breathe-light-secondary/10 rounded-md text-breathe-dark-primary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary"
          :aria-label="'Previous month'"
        >
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
            <path
              fill-rule="evenodd"
              d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z"
              clip-rule="evenodd"
            />
          </svg>
        </button>

        <h4 class="text-lg font-medium text-breathe-dark-primary">
          {{ currentMonthName }} {{ currentYear }}
        </h4>

        <button
          @click="nextMonth"
          class="p-2 hover:bg-breathe-light-secondary/10 rounded-md text-breathe-dark-primary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary"
          :aria-label="'Next month'"
        >
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
            <path
              fill-rule="evenodd"
              d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z"
              clip-rule="evenodd"
            />
          </svg>
        </button>
      </div>

      <!-- Weekday Headers -->
      <div class="grid grid-cols-7 mb-2">
        <span
          v-for="day in weekDays"
          :key="day"
          class="text-center text-sm font-medium text-breathe-dark-secondary py-2"
        >
          {{ day }}
        </span>
      </div>

      <!-- Calendar Grid -->
      <div class="grid grid-cols-7 gap-1">
        <button
          v-for="date in calendarDates"
          :key="date.toISOString()"
          @click="selectDate(date)"
          :disabled="isDisabled(date)"
          :class="[
            'w-full aspect-square flex items-center justify-center rounded-md text-sm focus:outline-none focus:ring-2 focus:ring-breathe-light-primary',
            isSelected(date)
              ? 'bg-breathe-dark-tertiary text-white'
              : isToday(date)
              ? 'bg-breathe-light-primary/10 text-breathe-dark-primary'
              : isDisabled(date)
              ? 'text-breathe-light-secondary cursor-not-allowed'
              : 'text-breathe-dark-primary hover:bg-breathe-light-secondary/10',
            !isCurrentMonth(date) && 'opacity-50'
          ]"
          :aria-label="formatDateLabel(date)"
          :aria-pressed="isSelected(date)"
        >
          {{ date.getDate() }}
        </button>
      </div>

      <!-- Selected Date Display -->
      <div v-if="selectedDate" class="mt-4 pt-4 border-t border-breathe-light-secondary">
        <p class="text-sm text-breathe-dark-secondary">
          Selected:
          <span class="font-medium text-breathe-dark-primary">
            {{ formatDate(selectedDate) }}
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SelectADate',
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
      type: Date,
      default: null
    },
    minDate: {
      type: Date,
      default: () => new Date()
    },
    maxDate: {
      type: Date,
      default: () => {
        const date = new Date()
        date.setMonth(date.getMonth() + 3)
        return date
      }
    },
    disabledDates: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      currentDate: new Date(),
      selectedDate: this.value,
      weekDays: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
    }
  },
  computed: {
    currentMonthName() {
      return this.currentDate.toLocaleString('default', { month: 'long' })
    },
    currentYear() {
      return this.currentDate.getFullYear()
    },
    calendarDates() {
      const dates = []
      const firstDay = new Date(
        this.currentDate.getFullYear(),
        this.currentDate.getMonth(),
        1
      )
      const lastDay = new Date(
        this.currentDate.getFullYear(),
        this.currentDate.getMonth() + 1,
        0
      )

      // Add days from previous month
      const firstDayOfWeek = firstDay.getDay()
      for (let i = firstDayOfWeek - 1; i >= 0; i--) {
        const date = new Date(firstDay)
        date.setDate(date.getDate() - i - 1)
        dates.push(date)
      }

      // Add days from current month
      for (let i = 1; i <= lastDay.getDate(); i++) {
        const date = new Date(
          this.currentDate.getFullYear(),
          this.currentDate.getMonth(),
          i
        )
        dates.push(date)
      }

      // Add days from next month
      const remainingDays = 42 - dates.length // 6 rows × 7 days
      for (let i = 1; i <= remainingDays; i++) {
        const date = new Date(lastDay)
        date.setDate(date.getDate() + i)
        dates.push(date)
      }

      return dates
    }
  },
  methods: {
    previousMonth() {
      const date = new Date(this.currentDate)
      date.setMonth(date.getMonth() - 1)
      this.currentDate = date
    },
    nextMonth() {
      const date = new Date(this.currentDate)
      date.setMonth(date.getMonth() + 1)
      this.currentDate = date
    },
    selectDate(date) {
      if (!this.isDisabled(date)) {
        this.selectedDate = date
        this.$emit('input', date)
      }
    },
    isSelected(date) {
      return (
        this.selectedDate &&
        date.toDateString() === this.selectedDate.toDateString()
      )
    },
    isToday(date) {
      return date.toDateString() === new Date().toDateString()
    },
    isCurrentMonth(date) {
      return date.getMonth() === this.currentDate.getMonth()
    },
    isDisabled(date) {
      return (
        date < this.minDate ||
        date > this.maxDate ||
        this.disabledDates.some(
          (disabled) => disabled.toDateString() === date.toDateString()
        )
      )
    },
    formatDate(date) {
      return date.toLocaleDateString('en-US', {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      })
    },
    formatDateLabel(date) {
      return `${this.formatDate(date)}${
        this.isDisabled(date) ? ' (unavailable)' : ''
      }`
    }
  },
  watch: {
    value(newValue) {
      this.selectedDate = newValue
    }
  }
}
</script>

<style scoped>
.select-a-date {
  /* Secondary Light for borders */
  --calendar-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --calendar-text: var(--breathe-dark-primary);
  /* Primary Light for focus rings */
  --calendar-focus: var(--breathe-light-primary);
  /* Tertiary Dark for selected date */
  --calendar-selected: var(--breathe-dark-tertiary);
}
</style>
