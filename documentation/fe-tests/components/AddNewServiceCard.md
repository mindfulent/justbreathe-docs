# AddNewServiceCard Component Documentation

## Overview
The AddNewServiceCard component is a reusable card component that provides an interface for adding new services. It maintains consistent styling with the project's design system and implements full accessibility support.

## Component Features
- Responsive design adapting to mobile and desktop views
- Accessible form inputs with proper labeling
- Interactive hover and focus states
- Toast notifications for success/error feedback
- Keyboard navigation support

## Styling Implementation
- Uses Tailwind classes for consistent theming
- Implements project color palette:
  - Background: breathe-light-primary
  - Text: breathe-dark-primary
  - Interactive elements: breathe-dark-tertiary
- Maintains WCAG 2.1 Level AA compliance

## Accessibility Features
- Semantic HTML structure
- ARIA labels and roles
- Focus management
- Screen reader support
- Keyboard navigation
- High contrast text

## Test Coverage

### Unit Tests
```typescript
import { describe, it, expect } from 'vitest'
import { render, fireEvent, screen } from '@testing-library/vue'
import AddNewServiceCard from '@/components/AddNewServiceCard.vue'

describe('AddNewServiceCard', () => {
  describe('Component Rendering', () => {
    it('renders with proper styling classes', () => {
      const { container } = render(AddNewServiceCard)
      expect(container.firstChild).toHaveClass('bg-breathe-light-primary')
      expect(container.firstChild).toHaveClass('rounded-lg')
    })
  })

  describe('Accessibility', () => {
    it('has proper ARIA attributes', () => {
      render(AddNewServiceCard)
      const addButton = screen.getByRole('button', { name: /add new service/i })
      expect(addButton).toHaveAttribute('aria-label')
    })

    it('maintains focus management', async () => {
      render(AddNewServiceCard)
      const addButton = screen.getByRole('button', { name: /add new service/i })
      await fireEvent.click(addButton)
      const form = screen.getByRole('form')
      expect(form).toHaveFocus()
    })

    it('supports keyboard navigation', async () => {
      render(AddNewServiceCard)
      const addButton = screen.getByRole('button', { name: /add new service/i })
      await fireEvent.keyDown(addButton, { key: 'Enter' })
      expect(screen.getByRole('form')).toBeVisible()
    })
  })

  describe('Interactivity', () => {
    it('shows hover state on interactive elements', async () => {
      render(AddNewServiceCard)
      const addButton = screen.getByRole('button', { name: /add new service/i })
      await fireEvent.mouseOver(addButton)
      expect(addButton).toHaveClass('hover:bg-breathe-dark-tertiary')
    })

    it('displays success toast on successful service addition', async () => {
      const { getByRole, emitted } = render(AddNewServiceCard)
      const addButton = getByRole('button', { name: /add new service/i })
      await fireEvent.click(addButton)
      // Fill form and submit
      await fireEvent.submit(getByRole('form'))
      expect(emitted()).toHaveProperty('service-added')
    })
  })

  describe('Responsive Behavior', () => {
    it('adapts to mobile view', () => {
      const { container } = render(AddNewServiceCard)
      // Test mobile-specific classes
      expect(container.firstChild).toHaveClass('md:w-full')
    })

    it('adapts to desktop view', () => {
      const { container } = render(AddNewServiceCard)
      // Test desktop-specific classes
      expect(container.firstChild).toHaveClass('lg:w-1/3')
    })
  })
})
```

### Test Cases Overview
1. **Component Rendering**
   - Proper styling classes application
   - Responsive layout classes
   - Dynamic class updates

2. **Accessibility Testing**
   - ARIA attributes presence
   - Keyboard navigation
   - Focus management
   - Screen reader compatibility

3. **Interactive Behavior**
   - Hover states
   - Click events
   - Form submission
   - Toast notifications

4. **Responsive Design**
   - Mobile view adaptation
   - Desktop view adaptation
   - Layout transitions

## Running Tests
```bash
# Run component tests
npm run test AddNewServiceCard.spec.ts

# Run with coverage
npm run test:coverage AddNewServiceCard.spec.ts

# Run in watch mode
npm run test:watch AddNewServiceCard.spec.ts
``` 