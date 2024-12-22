<template>
  <!-- Example footer bar structure with new color palette -->
  <footer class="bg-breathe-dark-secondary text-white py-8">
    <div class="container mx-auto px-4">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
        <!-- Company Info -->
        <div>
          <h2 class="text-breathe-light-primary text-xl font-bold mb-4">JustBreathe</h2>
          <p class="text-white/80">Empowering wellness professionals with seamless scheduling solutions.</p>
        </div>
        
        <!-- Quick Links -->
        <div>
          <h3 class="text-breathe-light-primary font-bold mb-4">Quick Links</h3>
          <ul class="space-y-2">
            <li><a href="#" class="text-white/80 hover:text-white transition-colors">About Us</a></li>
            <li><a href="#" class="text-white/80 hover:text-white transition-colors">Services</a></li>
            <li><a href="#" class="text-white/80 hover:text-white transition-colors">Contact</a></li>
          </ul>
        </div>
        
        <!-- Contact -->
        <div>
          <h3 class="text-breathe-light-primary font-bold mb-4">Contact Us</h3>
          <button class="bg-breathe-dark-tertiary text-white px-4 py-2 rounded-md hover:bg-opacity-90 transition-colors">
            Get in Touch
          </button>
        </div>
      </div>
      
      <!-- Copyright -->
      <div class="mt-8 pt-8 border-t border-breathe-light-secondary text-center">
        <p class="text-white/80">&copy; 2024 JustBreathe. All rights reserved.</p>
      </div>
    </div>
  </footer>
</template>

<style scoped>
/* Example of using CSS variables for dynamic styling */
.custom-footer-element {
  border-color: var(--breathe-light-secondary);
  background-color: var(--breathe-dark-secondary);
}
</style>
