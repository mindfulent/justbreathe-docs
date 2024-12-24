# ServicePublicCard Tests

## Overview
This document outlines the test coverage for the `ServicePublicCard` component, which displays a service's details and booking options in a public profile context.

## Test Categories

### Component Rendering
- ✅ Renders service information correctly (name, description, category)
- ✅ Renders all service options with durations and fees
- ✅ Handles missing optional fields gracefully

### Accessibility
- ✅ Uses semantic heading (h3) for service name
- ✅ Provides descriptive button labels for booking options
- ✅ Maintains proper text contrast with Breathe color palette

### Interactivity
- ✅ Handles booking service option clicks
- ✅ Logs appropriate debug information
- ✅ Triggers correct events

### Fee Formatting
- ✅ Formats numeric fees correctly
- ✅ Formats string fees correctly
- ✅ Maintains consistent currency display

### Responsive Design
- ✅ Maintains proper layout structure
- ✅ Adapts to mobile and desktop views
- ✅ Uses correct Tailwind classes for responsiveness

## Test Setup

### Mock Dependencies
```typescript
// Mock logger
vi.mock('@/utils/logger', () => ({
  logger: {
    debug: vi.fn(),
    info: vi.fn()
  }
}))
```

### Mock Data
```typescript
const mockService = {
  id: 'service1',
  name: 'Meditation Session',
  description: 'Guided meditation session',
  category: 'Wellness',
  options: [
    {
      id: 'opt1',
      duration: 60,
      fee: '75.00'
    },
    {
      id: 'opt2',
      duration: 30,
      fee: '45.00'
    }
  ]
}
```

## Key Test Considerations

### Text Content Testing
- Uses regex for split text content
- Handles both complete and partial text matches
- Tests for proper formatting

### Layout Testing
- Verifies responsive classes
- Tests container structure
- Checks proper nesting

### Accessibility Testing
- Verifies heading levels
- Tests button accessibility
- Checks ARIA labels

### Event Handling
- Tests click events
- Verifies logging
- Checks event payloads

## Example Tests

### Testing Split Text Content
```typescript
it('renders all service options', () => {
  render(ServicePublicCard, {
    props: {
      service: mockService
    }
  })

  // Check for duration text
  expect(screen.getByText(/60 minutes/)).toBeInTheDocument()
  expect(screen.getByText(/30 minutes/)).toBeInTheDocument()
  
  // Check for fee text
  expect(screen.getByText(/\$75\.00/)).toBeInTheDocument()
  expect(screen.getByText(/\$45\.00/)).toBeInTheDocument()
})
```

### Testing Responsive Layout
```typescript
it('maintains proper layout structure', () => {
  render(ServicePublicCard, {
    props: {
      service: mockService
    }
  })

  const optionsContainer = screen.getByText(/60 minutes/)
    .closest('div')
    ?.parentElement
    ?.closest('.flex-col.sm\\:flex-row')

  expect(optionsContainer).toHaveClass('flex-col', 'sm:flex-row')
}) 