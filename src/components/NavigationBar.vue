<template>
  <!-- Example navigation bar structure with new color palette -->
  <nav class="bg-white border-b border-breathe-light-secondary p-4">
    <div class="flex justify-between items-center">
      <h1 class="text-breathe-dark-primary text-xl font-bold">JustBreathe</h1>
      <div class="flex space-x-4">
        <a href="#" class="text-breathe-light-primary hover:text-breathe-dark-primary transition-colors">Home</a>
        <a href="#" class="text-breathe-light-primary hover:text-breathe-dark-primary transition-colors">Services</a>
        <a href="#" class="text-breathe-light-primary hover:text-breathe-dark-primary transition-colors">About</a>
        <button class="bg-breathe-dark-tertiary text-white px-4 py-2 rounded-md hover:bg-opacity-90 transition-colors">
          Sign In
        </button>
      </div>
    </div>
  </nav>
</template>

<style scoped>
/* Example of using CSS variables for dynamic styling */
.custom-nav-element {
  color: var(--breathe-dark-primary);
  border-color: var(--breathe-light-secondary);
}
</style>
