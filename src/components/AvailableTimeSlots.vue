<template>
  <div class="w-full">
    <!-- Header -->
    <div class="mb-4">
      <h3 class="text-lg font-semibold text-breathe-dark-primary">
        {{ label || 'Available Time Slots' }}
      </h3>
      <p v-if="description" class="mt-1 text-sm text-breathe-dark-secondary">
        {{ description }}
      </p>
    </div>

    <!-- Time Slots Grid -->
    <div class="border border-breathe-light-secondary rounded-lg bg-white p-4">
      <!-- Date Navigation -->
      <div class="flex items-center justify-between mb-4">
        <button
          @click="previousDay"
          class="p-2 hover:bg-breathe-light-secondary/10 rounded-md text-breathe-dark-primary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary"
          :aria-label="'Previous day'"
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
          {{ formatDate(currentDate) }}
        </h4>

        <button
          @click="nextDay"
          class="p-2 hover:bg-breathe-light-secondary/10 rounded-md text-breathe-dark-primary focus:outline-none focus:ring-2 focus:ring-breathe-light-primary"
          :aria-label="'Next day'"
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

      <!-- Time Slots -->
      <div class="grid grid-cols-3 gap-3">
        <button
          v-for="slot in availableSlots"
          :key="slot.time"
          @click="selectTimeSlot(slot)"
          :disabled="!slot.available"
          :class="[
            'p-3 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-breathe-light-primary',
            isSelected(slot)
              ? 'bg-breathe-dark-tertiary text-white'
              : slot.available
              ? 'bg-white border border-breathe-light-secondary text-breathe-dark-primary hover:bg-breathe-light-secondary/10'
              : 'bg-gray-50 border border-breathe-light-secondary text-breathe-light-secondary cursor-not-allowed'
          ]"
          :aria-label="formatTimeSlotLabel(slot)"
          :aria-pressed="isSelected(slot)"
        >
          <div class="flex flex-col items-center">
            <span class="font-medium">{{ formatTime(slot.time) }}</span>
            <span
              class="text-xs mt-1"
              :class="{
                'text-white': isSelected(slot),
                'text-breathe-dark-secondary': !isSelected(slot) && slot.available,
                'text-breathe-light-secondary': !slot.available
              }"
            >
              {{ slot.duration }} min
            </span>
          </div>
        </button>
      </div>

      <!-- Empty State -->
      <div
        v-if="availableSlots.length === 0"
        class="text-center py-8 text-breathe-dark-secondary"
      >
        No available time slots for this day.
        <br />
        Please try another date.
      </div>

      <!-- Selected Time Display -->
      <div
        v-if="selectedSlot"
        class="mt-4 pt-4 border-t border-breathe-light-secondary"
      >
        <p class="text-sm text-breathe-dark-secondary">
          Selected Time:
          <span class="font-medium text-breathe-dark-primary">
            {{ formatDate(currentDate) }} at {{ formatTime(selectedSlot.time) }}
            ({{ selectedSlot.duration }} minutes)
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AvailableTimeSlots',
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
    },
    minDate: {
      type: Date,
      default: () => new Date()
    },
    maxDate: {
      type: Date,
      default: () => {
        const date = new Date()
        date.setMonth(date.getMonth() + 1)
        return date
      }
    }
  },
  data() {
    return {
      currentDate: new Date(),
      selectedSlot: this.value,
      mockTimeSlots: [
        { time: '09:00', duration: 30, available: true },
        { time: '09:30', duration: 30, available: false },
        { time: '10:00', duration: 60, available: true },
        { time: '11:00', duration: 30, available: true },
        { time: '11:30', duration: 30, available: true },
        { time: '12:00', duration: 60, available: false },
        { time: '13:00', duration: 30, available: true },
        { time: '13:30', duration: 30, available: true },
        { time: '14:00', duration: 60, available: true },
        { time: '15:00', duration: 30, available: false },
        { time: '15:30', duration: 30, available: true },
        { time: '16:00', duration: 60, available: true }
      ]
    }
  },
  computed: {
    availableSlots() {
      // In a real implementation, this would fetch slots from an API
      // based on the currentDate
      return this.mockTimeSlots
    }
  },
  methods: {
    previousDay() {
      const date = new Date(this.currentDate)
      date.setDate(date.getDate() - 1)
      if (date >= this.minDate) {
        this.currentDate = date
        this.selectedSlot = null
      }
    },
    nextDay() {
      const date = new Date(this.currentDate)
      date.setDate(date.getDate() + 1)
      if (date <= this.maxDate) {
        this.currentDate = date
        this.selectedSlot = null
      }
    },
    selectTimeSlot(slot) {
      if (slot.available) {
        this.selectedSlot = slot
        this.$emit('input', {
          ...slot,
          date: this.currentDate
        })
      }
    },
    isSelected(slot) {
      return (
        this.selectedSlot &&
        this.selectedSlot.time === slot.time
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
    formatTime(time) {
      // Convert 24h time to 12h format
      const [hours, minutes] = time.split(':')
      const hour = parseInt(hours)
      const ampm = hour >= 12 ? 'PM' : 'AM'
      const hour12 = hour % 12 || 12
      return `${hour12}:${minutes} ${ampm}`
    },
    formatTimeSlotLabel(slot) {
      return `${this.formatTime(slot.time)} - ${slot.duration} minutes${
        !slot.available ? ' (unavailable)' : ''
      }`
    }
  },
  watch: {
    value(newValue) {
      this.selectedSlot = newValue
    }
  }
}
</script>

<style scoped>
.available-time-slots {
  /* Secondary Light for borders */
  --timeslot-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --timeslot-text: var(--breathe-dark-primary);
  /* Primary Light for focus rings */
  --timeslot-focus: var(--breathe-light-primary);
  /* Tertiary Dark for selected slot */
  --timeslot-selected: var(--breathe-dark-tertiary);
}
</style>
