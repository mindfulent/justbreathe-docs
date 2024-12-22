<template>
  <form
    class="max-w-2xl mx-auto p-6 bg-white rounded-lg shadow-md border border-breathe-light-secondary"
    @submit.prevent="handleSubmit"
  >
    <!-- Name Field -->
    <div class="mb-6">
      <label
        for="name"
        class="block text-breathe-dark-primary font-medium mb-2"
      >
        Full Name
      </label>
      <input
        id="name"
        v-model="formData.name"
        type="text"
        :class="[
          'w-full px-4 py-2 rounded-md',
          'border border-breathe-light-secondary',
          'focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent',
          'text-breathe-dark-primary placeholder-breathe-light-primary/60',
          errors.name ? 'border-red-500' : ''
        ]"
        placeholder="Enter your full name"
        required
      />
      <p v-if="errors.name" class="mt-1 text-sm text-red-600">
        {{ errors.name }}
      </p>
    </div>

    <!-- Email Field -->
    <div class="mb-6">
      <label
        for="email"
        class="block text-breathe-dark-primary font-medium mb-2"
      >
        Email Address
      </label>
      <input
        id="email"
        v-model="formData.email"
        type="email"
        :class="[
          'w-full px-4 py-2 rounded-md',
          'border border-breathe-light-secondary',
          'focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent',
          'text-breathe-dark-primary placeholder-breathe-light-primary/60',
          errors.email ? 'border-red-500' : ''
        ]"
        placeholder="Enter your email address"
        required
      />
      <p v-if="errors.email" class="mt-1 text-sm text-red-600">
        {{ errors.email }}
      </p>
    </div>

    <!-- Phone Field -->
    <div class="mb-6">
      <label
        for="phone"
        class="block text-breathe-dark-primary font-medium mb-2"
      >
        Phone Number
      </label>
      <input
        id="phone"
        v-model="formData.phone"
        type="tel"
        :class="[
          'w-full px-4 py-2 rounded-md',
          'border border-breathe-light-secondary',
          'focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent',
          'text-breathe-dark-primary placeholder-breathe-light-primary/60',
          errors.phone ? 'border-red-500' : ''
        ]"
        placeholder="Enter your phone number"
        required
      />
      <p v-if="errors.phone" class="mt-1 text-sm text-red-600">
        {{ errors.phone }}
      </p>
    </div>

    <!-- Preferred Contact Method -->
    <div class="mb-6">
      <span class="block text-breathe-dark-primary font-medium mb-2">
        Preferred Contact Method
      </span>
      <div class="space-y-2">
        <label
          v-for="method in contactMethods"
          :key="method.value"
          class="flex items-center"
        >
          <input
            type="radio"
            :value="method.value"
            v-model="formData.contactMethod"
            :class="[
              'form-radio',
              'text-breathe-dark-tertiary',
              'border-breathe-light-secondary',
              'focus:ring-breathe-light-primary'
            ]"
            required
          />
          <span class="ml-2 text-breathe-dark-primary">{{ method.label }}</span>
        </label>
      </div>
    </div>

    <!-- Message Field -->
    <div class="mb-6">
      <label
        for="message"
        class="block text-breathe-dark-primary font-medium mb-2"
      >
        Additional Notes
      </label>
      <textarea
        id="message"
        v-model="formData.message"
        :class="[
          'w-full px-4 py-2 rounded-md',
          'border border-breathe-light-secondary',
          'focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:border-transparent',
          'text-breathe-dark-primary placeholder-breathe-light-primary/60',
          'resize-none',
          errors.message ? 'border-red-500' : ''
        ]"
        rows="4"
        placeholder="Any additional information you'd like to share"
      ></textarea>
      <p v-if="errors.message" class="mt-1 text-sm text-red-600">
        {{ errors.message }}
      </p>
    </div>

    <!-- Submit Button -->
    <button
      type="submit"
      :class="[
        'w-full px-6 py-3 rounded-md',
        'bg-breathe-dark-tertiary text-white',
        'hover:bg-breathe-dark-tertiary/90',
        'focus:outline-none focus:ring-2 focus:ring-breathe-light-primary focus:ring-offset-2',
        'transition-colors duration-200',
        'font-medium',
        isSubmitting ? 'opacity-75 cursor-not-allowed' : ''
      ]"
      :disabled="isSubmitting"
    >
      {{ isSubmitting ? 'Submitting...' : 'Submit' }}
    </button>
  </form>
</template>

<script>
export default {
  name: 'IntakeForm',
  data() {
    return {
      formData: {
        name: '',
        email: '',
        phone: '',
        contactMethod: '',
        message: ''
      },
      errors: {},
      isSubmitting: false,
      contactMethods: [
        { label: 'Email', value: 'email' },
        { label: 'Phone', value: 'phone' },
        { label: 'Either', value: 'either' }
      ]
    }
  },
  methods: {
    validateForm() {
      this.errors = {}
      
      if (!this.formData.name.trim()) {
        this.errors.name = 'Name is required'
      }
      
      if (!this.formData.email.trim()) {
        this.errors.email = 'Email is required'
      } else if (!this.isValidEmail(this.formData.email)) {
        this.errors.email = 'Please enter a valid email address'
      }
      
      if (!this.formData.phone.trim()) {
        this.errors.phone = 'Phone number is required'
      } else if (!this.isValidPhone(this.formData.phone)) {
        this.errors.phone = 'Please enter a valid phone number'
      }
      
      return Object.keys(this.errors).length === 0
    },
    isValidEmail(email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return re.test(email)
    },
    isValidPhone(phone) {
      const re = /^\+?[\d\s-()]{10,}$/
      return re.test(phone)
    },
    async handleSubmit() {
      if (this.isSubmitting) return
      
      if (!this.validateForm()) {
        return
      }
      
      this.isSubmitting = true
      
      try {
        // Emit form data to parent
        this.$emit('submit', { ...this.formData })
        
        // Reset form after successful submission
        this.formData = {
          name: '',
          email: '',
          phone: '',
          contactMethod: '',
          message: ''
        }
        this.errors = {}
      } catch (error) {
        console.error('Form submission error:', error)
      } finally {
        this.isSubmitting = false
      }
    }
  }
}
</script>

<style scoped>
/* Example of using CSS variables for dynamic styling */
.intake-form {
  /* Secondary Light for borders */
  --form-border: var(--breathe-light-secondary);
  /* Primary Dark for text */
  --form-text: var(--breathe-dark-primary);
  /* Primary Light for focus rings */
  --focus-ring: var(--breathe-light-primary);
  /* Tertiary Dark for submit button */
  --submit-bg: var(--breathe-dark-tertiary);
}

/* Custom radio button styling */
.form-radio {
  @apply h-4 w-4;
  @apply border-2;
  @apply rounded-full;
  @apply transition-colors duration-200;
}

.form-radio:checked {
  background-image: url("data:image/svg+xml,%3csvg viewBox='0 0 16 16' fill='white' xmlns='http://www.w3.org/2000/svg'%3e%3ccircle cx='8' cy='8' r='3'/%3e%3c/svg%3e");
}
</style>
