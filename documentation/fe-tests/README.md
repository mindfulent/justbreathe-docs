# Frontend Testing Documentation

This directory contains detailed testing documentation for each Vue component in the Just Breathe application. Each component has its own dedicated documentation file that covers test setup, testing categories, and best practices.

## Directory Structure

- `fe-tests/` - Frontend testing documentation
  - `cards/` - Authentication-related components
  - `components/` - Navigation-related components
  - `pages/` - Page components  
- `README.md` - This file

## Testing Framework and Setup

### Core Testing Stack
- Vitest as the primary testing framework
- @testing-library/vue for component testing
- @testing-library/jest-dom for enhanced DOM assertions
- @vue/test-utils for Vue component testing
- @pinia/testing for Pinia store testing

### Available Commands
```bash
npm run test           # Run tests in watch mode
npm run test:coverage  # Run tests with coverage report
npm run test:ui        # Run tests with UI interface
npm run test:health    # Run API health check tests
```

### Test Location Convention
- Tests should be placed in `__tests__` directories next to the code being tested
- Test files should follow the naming pattern: `*.{test,spec}.{js,mjs,cjs,ts,mts,cts,jsx,tsx}`

## Testing Requirements

### Unit Test Coverage
Each component must include tests for:

1. **Functional Styling**
   - Component transitions that affect user interaction
   - Critical responsive behavior that affects functionality
   - Dynamic class applications (e.g., active states, loading states)

2. **Accessibility Features**
   - ARIA attributes presence and correctness
   - Keyboard navigation paths
   - Screen reader text content
   - Focus management
   - Color contrast compliance

3. **Interactive Behavior**
   - User interaction flows
   - State management
   - Event handling
   - Responsive adaptations

### Testing Best Practices

1. **Don't Test Static Styling**
   - Skip testing of static color applications
   - Skip testing of purely visual classes
   - Skip testing of basic layout classes
   
2. **Focus on Functional Changes**
   - Test class changes that affect user interaction
   - Test responsive behavior that changes functionality
   - Test dynamic style updates based on component state

3. **Prioritize User Experience**
   - Test transitions that provide user feedback
   - Test responsive adaptations that affect usability
   - Test state-based style changes that indicate component status

### Accessibility Testing
1. **Manual Testing**
   - Screen reader compatibility
   - Keyboard navigation
   - Focus management
   - Color contrast

2. **Automated Testing**
   - ARIA attributes and roles
   - Semantic HTML structure
   - Interactive element accessibility
   - Focus order and management

## Common Testing Patterns

### Finding Elements (Using Semantic Queries)
```typescript
// Preferred
getByRole('button', { name: 'Submit' })
getByLabelText('Email address')
getByText('Welcome')

// Avoid
querySelector('.submit-button')
querySelector('input[type="email"]')
```

### Testing Keyboard Navigation
```typescript
await fireEvent.keyDown(element, { key: 'Tab' })
await fireEvent.keyDown(element, { key: 'Enter' })
```

### Testing Focus Management
```typescript
const trigger = getByRole('button')
await fireEvent.click(trigger)
expect(getByRole('dialog')).toHaveFocus()
```

### Testing ARIA States
```typescript
expect(element).toHaveAttribute('aria-expanded', 'true')
expect(element).toHaveAttribute('aria-hidden', 'false')
```

### Testing Component Mounting
```typescript
// Basic component mount
const wrapper = mount(Component)

// With dependencies
const wrapper = mountWithDeps(Component, {
  global: {
    plugins: [router, pinia],
    stubs: {
      'RouterLink': true
    }
  }
})

// Wait for updates
await wrapper.vm.$nextTick()
```

### Testing Events and User Interaction
```typescript
// Click events
await fireEvent.click(element)
await wrapper.vm.$nextTick()

// Form input
await fireEvent.update(input, 'new value')
await wrapper.vm.$nextTick()

// Keyboard events
await fireEvent.keyDown(element, { key: 'Enter' })
await wrapper.vm.$nextTick()
```

## Test File Structure
```typescript
describe('Component Name', () => {
  describe('Component Rendering', () => {
    // Basic rendering tests
    // Responsive behavior tests
    // Style class applications
  })

  describe('Accessibility', () => {
    // ARIA attributes presence
    // Keyboard navigation
    // Screen reader text
    // Focus management
  })

  describe('Interactivity', () => {
    // User interactions
    // State changes
    // Event handling
  })

  describe('Responsive Behavior', () => {
    // Mobile vs desktop view
    // Component adaptations
  })
})
```

## Testing Utilities and Mocks
The project provides several utilities and mocks in `src/test/setup.ts`:
- IntersectionObserver mock
- ResizeObserver mock
- matchMedia mock
- localStorage mock

Additional testing utilities in `src/test/test-utils.ts`:
- Pinia store setup helpers
- Test router creation
- Mock API responses 