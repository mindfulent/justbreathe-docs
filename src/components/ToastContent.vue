<template>
  <!-- Toast notification component with new color palette -->
  <div
    :class="[
      'p-4 rounded-lg shadow-lg max-w-sm',
      typeClasses[type] || typeClasses.default
    ]"
    role="alert"
    aria-live="polite"
  >
    <!-- Icon section -->
    <div class="flex items-start">
      <div class="flex-shrink-0">
        <slot name="icon"></slot>
      </div>

      <!-- Content section -->
      <div class="ml-3 flex-1">
        <p
          v-if="title"
          class="text-breathe-dark-primary font-medium"
        >
          {{ title }}
        </p>
        <p
          :class="[
            'text-sm',
            title ? 'mt-1' : '',
            type === 'error' ? 'text-white' : 'text-breathe-dark-primary'
          ]"
        >
          <slot></slot>
        </p>
      </div>

      <!-- Close button -->
      <div class="ml-4 flex-shrink-0 flex">
        <button
          @click="$emit('close')"
          class="inline-flex text-breathe-light-primary hover:text-breathe-dark-primary focus:outline-none focus:ring-2 focus:ring-breathe-light-secondary"
          aria-label="Close notification"
        >
          <slot name="close-icon">
            <span class="sr-only">Close</span>
            <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
              <path
                fill-rule="evenodd"
                d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                clip-rule="evenodd"
              />
            </svg>
          </slot>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ToastContent',
  props: {
    type: {
      type: String,
      default: 'default',
      validator: value => ['success', 'error', 'warning', 'info', 'default'].includes(value)
    },
    title: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      typeClasses: {
        success: 'bg-green-100 border border-breathe-light-secondary',
        error: 'bg-red-600 text-white', // Using standard red for error states instead of tertiary dark
        warning: 'bg-yellow-100 border border-breathe-light-secondary',
        info: 'bg-blue-100 border border-breathe-light-secondary',
        default: 'bg-white border border-breathe-light-secondary'
      }
    }
  }
}
</script>

<style scoped>
/* Example of using CSS variables for dynamic styling */
.toast-content {
  /* Secondary Light for borders */
  border-color: var(--breathe-light-secondary);
  /* Primary Dark for text */
  color: var(--breathe-dark-primary);
  /* Primary Light for interactive elements */
  --close-button-color: var(--breathe-light-primary);
  /* Standard red for error state */
  --error-bg: rgb(220, 38, 38); /* Tailwind red-600 */
}
</style>
