<template>
  <div
    class="relative bg-white rounded-lg shadow-md overflow-hidden border border-breathe-light-secondary hover:shadow-lg transition-shadow duration-200"
    :class="{ 'cursor-pointer': !disabled }"
    @click="!disabled && $emit('select', template)"
    @keydown.enter="!disabled && $emit('select', template)"
    tabindex="0"
    role="button"
    :aria-disabled="disabled"
  >
    <!-- Service Image -->
    <div class="relative h-48 bg-breathe-light-secondary/20">
      <img
        v-if="template.imageUrl"
        :src="template.imageUrl"
        :alt="template.name"
        class="w-full h-full object-cover"
      />
      <div
        v-else
        class="w-full h-full flex items-center justify-center text-breathe-light-primary"
      >
        <svg class="w-12 h-12" fill="currentColor" viewBox="0 0 24 24">
          <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H5V5h14v14z"/>
          <path d="M12 12c1.65 0 3-1.35 3-3s-1.35-3-3-3-3 1.35-3 3 1.35 3 3 3zm0-4c.55 0 1 .45 1 1s-.45 1-1 1-1-.45-1-1 .45-1 1-1z"/>
        </svg>
      </div>
    </div>

    <!-- Content -->
    <div class="p-4">
      <!-- Title -->
      <h3 class="text-lg font-semibold text-breathe-dark-primary mb-2">
        {{ template.name }}
      </h3>

      <!-- Description -->
      <p class="text-breathe-dark-secondary text-sm mb-4">
        {{ template.description }}
      </p>

      <!-- Duration and Price -->
      <div class="flex justify-between items-center text-sm">
        <span class="text-breathe-dark-secondary">
          {{ template.duration }} minutes
        </span>
        <span class="font-medium text-breathe-dark-primary">
          ${{ template.price.toFixed(2) }}
        </span>
      </div>

      <!-- Tags -->
      <div class="mt-3 flex flex-wrap gap-2">
        <span
          v-for="tag in template.tags"
          :key="tag"
          class="px-2 py-1 text-xs rounded-full bg-breathe-light-primary/10 text-breathe-dark-primary"
        >
          {{ tag }}
        </span>
      </div>
    </div>

    <!-- Select Button -->
    <div class="p-4 border-t border-breathe-light-secondary bg-white">
      <button
        class="w-full px-4 py-2 rounded-md text-white transition-colors duration-200"
        :class="[
          disabled
            ? 'bg-breathe-light-secondary cursor-not-allowed'
            : 'bg-breathe-dark-tertiary hover:bg-breathe-dark-tertiary/90 focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:ring-offset-2'
        ]"
        :disabled="disabled"
        @click.stop="!disabled && $emit('select', template)"
      >
        {{ disabled ? 'Unavailable' : 'Select Template' }}
      </button>
    </div>

    <!-- Disabled Overlay -->
    <div
      v-if="disabled"
      class="absolute inset-0 bg-white/60 backdrop-blur-sm"
      aria-hidden="true"
    ></div>
  </div>
</template>

<script>
export default {
  name: 'ServiceTemplateCard',
  props: {
    template: {
      type: Object,
      required: true,
      validator: (template) => {
        return (
          template.name &&
          template.description &&
          typeof template.duration === 'number' &&
          typeof template.price === 'number' &&
          Array.isArray(template.tags)
        )
      }
    },
    disabled: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style scoped>
.service-template-card {
  /* Secondary Light for borders */
  --card-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --card-text: var(--breathe-dark-primary);
  /* Primary Light for decorative elements */
  --card-decorative: var(--breathe-light-primary);
  /* Tertiary Dark for CTA button */
  --card-cta: var(--breathe-dark-tertiary);
}
</style>
