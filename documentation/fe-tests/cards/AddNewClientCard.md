# AddNewClientCard Component Tests

## Overview
The `AddNewClientCard` component tests cover form functionality, accessibility features, and user interactions for both add and edit modes.

## Test Structure

### Component Rendering
- Verifies correct rendering in add mode with all required fields
- Validates edit mode rendering with pre-populated client data
- Ensures proper display of form fields and buttons

### Accessibility Tests
- Validates ARIA labels and roles for form elements
- Tests keyboard navigation and focus management
- Verifies required field attributes
- Ensures proper button accessibility
- Tests Escape key functionality for cancellation

### Form Validation
- Tests required fields (name, email)
- Validates email format
- Checks form submission with invalid data
- Verifies validation state indicators

### Form Submission
- Tests successful form submission in add mode
- Validates emitted events with correct data structure
- Verifies cancel functionality
- Tests form submission in edit mode

### Edit Mode
- Validates pre-population of client data
- Tests client ID inclusion in emitted data
- Verifies proper update button text

## Test Cases

```typescript
describe('AddNewClientCard', () => {
  describe('Component Rendering', () => {
    // Tests basic rendering in add mode
    // Tests edit mode with populated data
  })

  describe('Accessibility', () => {
    // Tests ARIA attributes
    // Tests keyboard navigation
    // Tests focus management
  })

  describe('Form Validation', () => {
    // Tests required fields
    // Tests email validation
  })

  describe('Form Submission', () => {
    // Tests form submission events
    // Tests cancel functionality
  })

  describe('Edit Mode', () => {
    // Tests edit mode functionality
    // Tests client data handling
  })
})
```

## Key Features Tested

1. **Form Fields**
   - Name input (required)
   - Email input (required)
   - Phone input (optional)

2. **Validation**
   - Required field validation
   - Email format validation
   - Form submission validation

3. **Accessibility**
   - ARIA labels
   - Required field indicators
   - Focus management
   - Keyboard navigation
   - Escape key handling for cancellation

4. **Events**
   - submit-client event
   - cancel-add-client event

5. **Edit Mode**
   - Data pre-population
   - Update functionality
   - ID handling

## Test Coverage

The tests provide comprehensive coverage of:
- Component rendering
- Form validation
- Accessibility features
- User interactions
- Event handling
- Edit mode functionality

## Running the Tests

```bash
# Run all component tests
npm run test

# Run specific test file
npm run test AddNewClientCard.spec.ts

# Run with coverage
npm run test:coverage
``` 