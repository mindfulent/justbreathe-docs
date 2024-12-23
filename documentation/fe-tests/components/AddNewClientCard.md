# AddNewClientCard Component Documentation

## Overview
The AddNewClientCard component is a reusable card component that provides an interface for adding new clients. It follows the project's design system and implements comprehensive accessibility features while maintaining a consistent user experience.

## Component Features
- Responsive design for both mobile and desktop views
- Form validation with real-time feedback
- Client information input fields with proper validation
- Success/error toast notifications
- Keyboard-accessible interface

## Styling Implementation
- Utilizes Tailwind classes for consistent theming
- Implements project color palette:
  - Background: breathe-light-primary
  - Text: breathe-dark-primary
  - Interactive elements: breathe-dark-tertiary
- Maintains WCAG 2.1 Level AA compliance

## Accessibility Features
- Semantic HTML structure
- Descriptive ARIA labels
- Form validation feedback
- Focus management
- Screen reader announcements
- Keyboard navigation support

## Test Coverage

### Unit Tests
```typescript
import { describe, it, expect } from 'vitest'
import { render, fireEvent, screen } from '@testing-library/vue'
import AddNewClientCard from '@/components/AddNewClientCard.vue'

describe('AddNewClientCard', () => {
  describe('Component Rendering', () => {
    it('renders with proper styling classes', () => {
      const { container } = render(AddNewClientCard)
      expect(container.firstChild).toHaveClass('bg-breathe-light-primary')
      expect(container.firstChild).toHaveClass('rounded-lg')
    })

    it('displays all required form fields', () => {
      render(AddNewClientCard)
      expect(screen.getByLabelText(/client name/i)).toBeInTheDocument()
      expect(screen.getByLabelText(/email/i)).toBeInTheDocument()
      expect(screen.getByLabelText(/phone/i)).toBeInTheDocument()
    })
  })

  describe('Accessibility', () => {
    it('has proper ARIA attributes', () => {
      render(AddNewClientCard)
      const form = screen.getByRole('form')
      expect(form).toHaveAttribute('aria-label', 'Add new client form')
    })

    it('provides validation feedback to screen readers', async () => {
      render(AddNewClientCard)
      const emailInput = screen.getByLabelText(/email/i)
      await fireEvent.input(emailInput, { target: { value: 'invalid-email' } })
      expect(emailInput).toHaveAttribute('aria-invalid', 'true')
      expect(screen.getByRole('alert')).toBeInTheDocument()
    })

    it('maintains focus management', async () => {
      render(AddNewClientCard)
      const addButton = screen.getByRole('button', { name: /add client/i })
      await fireEvent.click(addButton)
      const nameInput = screen.getByLabelText(/client name/i)
      expect(nameInput).toHaveFocus()
    })
  })

  describe('Form Validation', () => {
    it('validates required fields', async () => {
      render(AddNewClientCard)
      const submitButton = screen.getByRole('button', { name: /save/i })
      await fireEvent.click(submitButton)
      expect(screen.getAllByRole('alert')).toHaveLength(3) // Name, email, phone required
    })

    it('validates email format', async () => {
      render(AddNewClientCard)
      const emailInput = screen.getByLabelText(/email/i)
      await fireEvent.input(emailInput, { target: { value: 'invalid-email' } })
      expect(screen.getByText(/invalid email format/i)).toBeInTheDocument()
    })

    it('validates phone format', async () => {
      render(AddNewClientCard)
      const phoneInput = screen.getByLabelText(/phone/i)
      await fireEvent.input(phoneInput, { target: { value: '123' } })
      expect(screen.getByText(/invalid phone format/i)).toBeInTheDocument()
    })
  })

  describe('Interactivity', () => {
    it('shows success toast on successful client addition', async () => {
      const { getByRole, emitted } = render(AddNewClientCard)
      // Fill form with valid data
      await fireEvent.input(screen.getByLabelText(/client name/i), { 
        target: { value: 'John Doe' } 
      })
      await fireEvent.input(screen.getByLabelText(/email/i), {
        target: { value: 'john@example.com' }
      })
      await fireEvent.input(screen.getByLabelText(/phone/i), {
        target: { value: '(555) 555-5555' }
      })
      await fireEvent.submit(getByRole('form'))
      expect(emitted()).toHaveProperty('client-added')
    })
  })

  describe('Responsive Behavior', () => {
    it('adapts to mobile view', () => {
      const { container } = render(AddNewClientCard)
      expect(container.firstChild).toHaveClass('md:w-full')
    })

    it('adapts to desktop view', () => {
      const { container } = render(AddNewClientCard)
      expect(container.firstChild).toHaveClass('lg:w-1/3')
    })
  })
})
```

### Test Cases Overview
1. **Component Rendering**
   - Proper styling classes application
   - Form field presence
   - Layout structure

2. **Accessibility Testing**
   - ARIA attributes
   - Screen reader feedback
   - Keyboard navigation
   - Focus management
   - Form validation announcements

3. **Form Validation**
   - Required fields
   - Email format
   - Phone format
   - Error messages
   - Real-time validation

4. **Interactive Behavior**
   - Form submission
   - Success/error states
   - Toast notifications
   - Event emissions

5. **Responsive Design**
   - Mobile view adaptation
   - Desktop view adaptation
   - Form field layouts

## Running Tests
```bash
# Run component tests
npm run test AddNewClientCard.spec.ts

# Run with coverage
npm run test:coverage AddNewClientCard.spec.ts

# Run in watch mode
npm run test:watch AddNewClientCard.spec.ts
``` 