# EmailTemplatesPage Tests

## Overview
The `EmailTemplatesPage.spec.ts` implements comprehensive testing for the email templates management page, covering component rendering, accessibility, interactivity, and responsive behavior.

## Test Structure

### Component Rendering Tests
```typescript
describe('Component Rendering', () => {
  it('renders the page title correctly')     // Verifies basic page structure
  it('shows loading state')                  // Tests loading state display
  it('shows empty state')                    // Tests empty state messaging
  it('renders templates grid')               // Tests template list display
})
```

### Accessibility Tests
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles')     // Verifies ARIA attributes
  it('maintains proper heading hierarchy')    // Tests heading structure
  it('ensures error messages are announced')  // Tests screen reader support
})
```

### Interactivity Tests
```typescript
describe('Interactivity', () => {
  it('toggles add email form')               // Tests form visibility
  it('handles template deletion flow')        // Tests deletion confirmation
})
```

### Responsive Tests
```typescript
describe('Responsive Behavior', () => {
  it('maintains proper button size')         // Tests mobile compatibility
  it('applies proper spacing in grid')       // Tests responsive layout
})
```

## Key Implementation Details

### Store Integration
- Uses `createTestingPinia` for store mocking
- Tests both success and error states
- Verifies store action calls

### Error Handling
```typescript
// Error state setup
const store = createTestingPinia({
  initialState: {
    emailTemplates: { 
      error: 'Test error message',
      templates: [],
      isLoading: false
    }
  }
})

// Error message verification
const alert = screen.getByRole('alert')
expect(alert).toHaveAttribute('aria-live', 'polite')
expect(alert).toHaveTextContent(/Test error message/)
```

### Accessibility Features
1. ARIA Roles:
   - `toolbar` for action buttons
   - `region` for content sections
   - `alert` for error messages
   - `list` for template grid

2. ARIA Labels:
   - "Email template management actions"
   - "Your email templates section"
   - "Loading email templates"
   - "List of your email templates"

3. Focus Management:
   - Proper button focus states
   - Form focus management
   - Modal focus trapping

### Component Isolation
- Uses component stubs where appropriate
- Tests component in isolation
- Verifies component interactions

## Best Practices Demonstrated
1. **Semantic Queries**: Uses role-based and semantic queries instead of class selectors
2. **State Management**: Properly tests different states (loading, empty, error, populated)
3. **Accessibility**: Comprehensive ARIA and screen reader support testing
4. **Error Handling**: Tests both success and error paths
5. **Responsive Design**: Verifies mobile and desktop layouts

## Example Usage
```typescript
// Mounting the component with store
const wrapper = render(EmailTemplatesPage, {
  global: {
    plugins: [createTestingPinia({
      initialState: {
        emailTemplates: { /* initial state */ }
      }
    })]
  }
})

// Testing user interactions
const addButton = screen.getByRole('button', { name: /Add Email Template/ })
await fireEvent.click(addButton)
const form = screen.getByRole('form', { name: /Add New Email Template/ })
expect(form).toBeTruthy()
``` 