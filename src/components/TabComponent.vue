<template>
  <!-- Example tab component structure with new color palette -->
  <div class="border-b border-breathe-light-secondary">
    <nav class="flex space-x-4" role="tablist">
      <button
        v-for="tab in tabs"
        :key="tab.id"
        :class="[
          'px-4 py-2 font-medium transition-colors',
          isActive(tab.id)
            ? 'text-breathe-dark-primary border-b-2 border-breathe-light-primary'
            : 'text-breathe-light-primary hover:text-breathe-dark-primary'
        ]"
        :aria-selected="isActive(tab.id)"
        role="tab"
        @click="selectTab(tab.id)"
      >
        {{ tab.label }}
      </button>
    </nav>
    <div class="p-4" role="tabpanel">
      <slot :name="activeTab"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabComponent',
  props: {
    tabs: {
      type: Array,
      required: true,
      validator: (tabs) => tabs.every((tab) => tab.id && tab.label)
    },
    initialTab: {
      type: String,
      default: null
    }
  },
  data() {
    return {
      activeTab: this.initialTab || (this.tabs[0] && this.tabs[0].id)
    }
  },
  methods: {
    isActive(tabId) {
      return this.activeTab === tabId
    },
    selectTab(tabId) {
      this.activeTab = tabId
      this.$emit('tab-changed', tabId)
    }
  }
}
</script>

<style scoped>
/* Example of using CSS variables for dynamic styling */
.custom-tab {
  /* Secondary Light for form borders */
  border-color: var(--breathe-light-secondary);
  /* Primary Dark for important text */
  color: var(--breathe-dark-primary);
  /* Primary Light for interactive indicators */
  --active-indicator: var(--breathe-light-primary);
}
</style>
