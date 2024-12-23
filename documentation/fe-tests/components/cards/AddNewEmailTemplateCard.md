# AddNewEmailTemplateCard Tests

## Overview
The `AddNewEmailTemplateCard.spec.ts` implements testing for the email template creation/editing form component. This component is critical for managing email templates in the application, with special attention to accessibility and form handling.

## Test Structure

### Component Rendering Tests
```typescript
describe('Component Rendering', () => {
  it('renders with proper structure and labels')    // Tests basic component layout
  it('shows correct title based on mode')           // Tests title changes between add/edit modes
  it('displays available variables help text')       // Tests help text visibility
})
```

### Accessibility Tests
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles')            // Tests ARIA attributes
  it('marks required fields appropriately')         // Tests required field attributes
  it('maintains proper focus management')           // Tests focus handling
})
```

### Form Interaction Tests
```typescript
describe('Form Interaction', () => {
  it('emits submit event with form data')          // Tests form submission
  it('emits cancel event when cancel clicked')      // Tests cancellation
})
```

### Edit Mode Tests
```typescript
describe('Edit Mode', () => {
  it('populates form with existing template data')  // Tests template data population
  it('shows correct button text in edit mode')      // Tests mode-specific button text
})
```

## Key Implementation Details

### Mock Data Setup
```typescript
const mockTemplate: Partial<EmailTemplate> = {
  name: 'Existing Template',
  subject: 'Existing Subject',
  body: 'Existing Body',
  is_default: false
}
```

### Component Props
- `showAddEmailForm`: Controls form visibility
- `isEditMode`: Toggles between create/edit modes
- `templateToEdit`: Optional template data for edit mode

### Store Integration
- Uses `createTestingPinia` for store mocking
- No direct store dependencies in tests

### Focus Management
```typescript
// Example of focus testing
nameInput.focus()
expect(document.activeElement).toBe(nameInput)
```

### Accessibility Features
1. ARIA Roles:
   - `form` for the form element
   - `note` for variable help text
   - Proper button labels

2. ARIA Labels:
   - Form context label (Add/Edit)
   - Submit/Cancel button labels
   - Required field indicators

3. Required Fields:
   - Name input
   - Subject input
   - Body textarea

### Component Features
1. Form Management:
   - Template name input
   - Subject line input
   - Body content textarea
   - Variable placeholders help text

2. Mode Switching:
   - Create mode (default)
   - Edit mode with pre-populated data

3. Event Emission:
   - `submit-email-template` with form data
   - `cancel-add-email-template` for cancellation

## Best Practices Demonstrated
1. **Clean Render Setup**: Reusable render function with default props
2. **Accessibility First**: Comprehensive ARIA testing
3. **Mode Handling**: Clear separation of create/edit modes
4. **Event Testing**: Proper event emission verification
5. **Focus Management**: Explicit focus testing
6. **Type Safety**: Proper TypeScript types for template data

## Example Usage
```typescript
// Basic component render
const { getByRole } = renderComponent()
expect(getByRole('form')).toBeInTheDocument()

// Edit mode testing
renderComponent({
  isEditMode: true,
  templateToEdit: mockTemplate
})
expect(getByLabelText('Name')).toHaveValue('Existing Template')
```

## Testing Considerations
1. **Form State**:
   - Test both empty and pre-filled states
   - Verify all form fields are properly controlled
   - Check required field validation

2. **Mode Switching**:
   - Verify correct title display
   - Check button text changes
   - Validate form data population

3. **Accessibility**:
   - Test all ARIA attributes
   - Verify focus management
   - Check required field indicators

4. **Event Handling**:
   - Test form submission
   - Verify cancellation
   - Check event payload structure 