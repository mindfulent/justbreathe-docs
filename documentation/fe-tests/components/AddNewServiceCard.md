# AddNewServiceCard Component Tests

## Overview
The `AddNewServiceCard` component tests cover form functionality, accessibility features, API integration, and user interactions for both add and edit modes.

## Test Structure

### Component Rendering
- Verifies correct rendering in add mode with all required fields
- Validates edit mode rendering with pre-populated service data
- Ensures proper display of form fields, options, and buttons

### Accessibility Tests
- Validates ARIA labels and roles for form elements
- Tests keyboard navigation and focus management
- Verifies required field attributes
- Ensures proper button accessibility

### Form Validation
- Tests required fields (name, description, category)
- Validates service options (duration and fee)
- Checks form submission with invalid data
- Verifies validation state indicators

### Service Options Management
- Tests adding multiple service options
- Validates removing service options
- Verifies fee input formatting
- Tests duration and fee validation

### Form Submission
- Tests successful form submission in add mode
- Validates emitted events with correct data structure
- Verifies cancel functionality
- Tests form submission in edit mode

### API Integration
- Tests email template fetching
- Tests intake form fetching
- Validates error handling for API calls
- Tests loading states

## Test Cases

```typescript
describe('AddNewServiceCard', () => {
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
    // Tests service options validation
  })

  describe('Service Options Management', () => {
    // Tests adding/removing options
    // Tests fee formatting
  })

  describe('Form Submission', () => {
    // Tests form submission events
    // Tests cancel functionality
  })

  describe('API Integration', () => {
    // Tests API calls
    // Tests error handling
  })
})
```

## Key Features Tested

1. **Form Fields**
   - Name input (required)
   - Description input (required)
   - Category input (required)
   - Email template selection
   - Intake form selection
   - Service requirements checkboxes

2. **Service Options**
   - Duration input
   - Fee input
   - Multiple options management
   - Fee formatting

3. **Validation**
   - Required field validation
   - Service options validation
   - Form submission validation

4. **Accessibility**
   - ARIA labels
   - Required field indicators
   - Focus management
   - Keyboard navigation

5. **Events**
   - submit-service event
   - cancel-add-service event

6. **API Integration**
   - Email templates fetching
   - Intake forms fetching
   - Error handling

## Test Coverage

The tests provide comprehensive coverage of:
- Component rendering
- Form validation
- Service options management
- Accessibility features
- User interactions
- Event handling
- API integration
- Error handling

## Running the Tests

```bash
# Run all component tests
npm run test

# Run specific test file
npm run test AddNewServiceCard.spec.ts

# Run with coverage
npm run test:coverage
```

## Mock Setup

The tests use mocked API responses for email templates and intake forms:

```typescript
vi.mock('@/utils/api', () => ({
  default: {
    get: vi.fn()
  }
}))

beforeEach(() => {
  vi.mocked(api.get).mockImplementation((url) => {
    if (url === '/email-templates/') {
      return Promise.resolve({ data: [] })
    }
    if (url === '/intake-forms/') {
      return Promise.resolve({ data: [] })
    }
    return Promise.reject(new Error('Not found'))
  })
})
``` 