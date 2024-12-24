# ClientsPage Component Documentation

## Overview
The ClientsPage component is a core feature that provides client management functionality. It allows users to view, create, edit, and delete client records while maintaining accessibility standards and proper user feedback.

## Component Structure

### Key Features
- Client listing with responsive grid layout
- Create new client functionality
- Edit existing client details
- Delete client with confirmation
- Loading states and empty states
- Accessibility compliance
- Error handling and logging

### Props
None - This is a page-level component

### Emits
None - Uses Pinia store for state management

### Dependencies
- `@/stores/clients` - Pinia store for client management
- `@/utils/logger` - Logging utility
- `@/components/cards/ClientCard.vue` - Client display component
- `@/components/cards/AddNewClientCard.vue` - Client form component
- `@/components/PromptModal.vue` - Confirmation dialog component

## Testing Strategy

### Test Categories

#### 1. Rendering Tests
```typescript
describe('Rendering', () => {
  it('displays the page title')
  it('shows loading state when isLoading is true')
  it('shows empty state when no clients are available')
  it('renders client cards for each client')
})
```

#### 2. Client Form Tests
```typescript
describe('Client Form', () => {
  it('toggles client form visibility when create button is clicked')
  it('shows edit form with selected client data')
})
```

#### 3. Client Action Tests
```typescript
describe('Client Actions', () => {
  it('handles client creation successfully')
  it('handles client update successfully')
  it('handles client deletion')
  it('cancels client deletion')
})
```

#### 4. Navigation Tests
```typescript
describe('Navigation', () => {
  it('navigates to client details page')
})
```

#### 5. Accessibility Tests
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles')
  it('has proper focus management')
})
```

#### 6. Error Handling Tests
```typescript
describe('Error Handling', () => {
  it('logs errors during client creation')
  it('logs errors during client deletion')
})
```

### Test Setup

```typescript
const mockClients = [
  {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    phone: '123-456-7890',
    total_bookings: 5,
    last_booking_date: '2024-01-01T12:00:00Z'
  },
  // ...
];

const mountComponent = (initialState = {}) => {
  return mount(ClientsPage, {
    global: {
      plugins: [
        createTestingPinia({
          createSpy: vi.fn,
          initialState: {
            clients: {
              clients: mockClients,
              isLoading: false,
              selectedClient: null,
              ...initialState
            }
          }
        })
      ],
      stubs: {
        ClientCard: {
          name: 'ClientCard',
          template: '<div class="client-card" data-test="client-card"><slot /></div>',
          props: ['client'],
          emits: ['delete', 'view-details']
        },
        // ... other stubs
      }
    }
  });
};
```

## Accessibility Implementation

### ARIA Roles and Labels
- `role="toolbar"` for action buttons container
- `role="region"` for client list section
- `role="status"` for loading and empty states
- Descriptive aria-labels on interactive elements

### Focus Management
- Proper focus trapping in modals
- Focus restoration after modal closure
- Visible focus indicators on interactive elements
- Logical tab order

### Keyboard Navigation
- Full keyboard accessibility for all actions
- Enter/Space for button activation
- Escape key handling for modals

## Error Handling

### Logged Events
1. Client Creation
   - Success: 'New client created successfully'
   - Error: 'Error in client submission'

2. Client Update
   - Success: 'Client updated successfully'
   - Error: 'Error in client submission'

3. Client Deletion
   - Success: 'Client deleted successfully'
   - Error: 'Error deleting client'

### Error Recovery
- Graceful error handling with user feedback
- State restoration on error
- Clear error messages
- Retry capabilities where appropriate

## Testing Commands

```bash
# Run all tests
npm run test

# Run specific test file
npm run test src/pages/__tests__/ClientsPage.spec.ts

# Run with coverage
npm run test:coverage
```

## Best Practices

1. **State Management**
   - Use Pinia store for client data
   - Keep local state minimal
   - Clear state on component unmount

2. **Performance**
   - Lazy loading of client data
   - Optimistic UI updates
   - Proper cleanup of event listeners

3. **Accessibility**
   - Semantic HTML structure
   - ARIA attributes for dynamic content
   - Keyboard navigation support
   - Screen reader compatibility

4. **Error Handling**
   - Comprehensive error logging
   - User-friendly error messages
   - Graceful degradation

## Style Implementation

### Color Scheme
- Uses breathe-dark-primary for headings
- breathe-dark-tertiary for action buttons
- breathe-light-secondary for borders
- White text on dark backgrounds

### Responsive Design
- Grid layout for client cards
- Stack layout on mobile
- Proper spacing and margins
- Touch-friendly targets

### Animation
- Smooth transitions for form visibility
- Loading state animations
- Modal transitions 