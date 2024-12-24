# ServicesPage Tests

## Overview
The `ServicesPage.spec.ts` implements testing for the services management page, covering service creation, template usage, accessibility, and error handling. This page has additional complexity due to its template-based service creation and CRUD operations.

## Test Structure

### Component Rendering Tests
```typescript
describe('Component Rendering', () => {
  it('renders with proper structure')        // Tests basic page layout
  it('shows empty state message')            // Tests empty state display
})
```

### Service Management Tests
```typescript
describe('Service Management', () => {
  it('creates service from template')        // Tests template-based creation
  it('displays error message')               // Tests error state handling
})
```

### Accessibility Tests
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles')     // Tests ARIA attributes
  it('maintains proper focus management')    // Tests focus handling
})
```

## Key Implementation Details

### Mock Data Setup
```typescript
const mockTemplate: ServiceTemplate = {
  id: 1,
  template_id: 'quick-chat',
  version: '1.0',
  name: "Quick Chat",
  description: "30 minutes for $30",
  icon: "📱",
  category: "Consultation",
  options: [{ duration: 30, fee: "30.00" }],
  email_template: '',
  intake_form: '',
  accessibility: { aria_label: "Template for Quick Chat" },
  is_system: false,
  usage_count: 0
}
```

### Store Integration
- Uses `createTestingPinia` with auth and services stores
- Tests service creation from templates
- Verifies error handling in store operations

### Focus Management
```typescript
it('maintains proper focus management', async () => {
  const { getByRole } = render(ServicesPage)
  const addButton = getByRole('button', { name: /Add Service/i })
  await fireEvent.click(addButton)
  
  // Test focus return after form close
  const cancelButton = getByRole('button', { name: /Cancel/i })
  await fireEvent.click(cancelButton)
  expect(document.activeElement?.getAttribute('aria-label'))
    .toBe('Add Service')
})
```

### Accessibility Features
1. ARIA Roles:
   - `toolbar` for service management actions
   - `region` for content sections
   - `alert` for error messages
   - `list` for service grid

2. ARIA Labels:
   - "Service management actions"
   - "Your services section"
   - "Add Service"
   - "Add Service via Template"

3. Focus Management:
   - Focus return after form closure
   - Button focus states
   - Modal focus trapping

### Component Features
1. Template Management:
   - Template selection and preview
   - Service creation from templates
   - Template grid display

2. Service CRUD Operations:
   - Create service (manual and template-based)
   - Edit existing services
   - Delete service with confirmation
   - Service listing and display

3. Error Handling:
   - Service creation errors
   - Template loading errors
   - API failure states

## Best Practices Demonstrated
1. **Mock Setup**: Comprehensive mock data for templates and services
2. **Timer Management**: Proper handling of timers for async operations
3. **Store Integration**: Testing with multiple stores (auth and services)
4. **Focus Management**: Testing focus return and keyboard navigation
5. **Error States**: Testing error message display and handling

## Example Usage
```typescript
// Basic component render with store
const { getByText, getByRole } = render(ServicesPage, {
  global: {
    plugins: [createTestingPinia({
      createSpy: vi.fn,
      initialState: {
        auth: { isAuthenticated: true, currentUser: { id: 1 } }
      }
    })]
  }
})

// Testing template-based service creation
const servicesStore = useServicesStore()
await servicesStore.createService(mockServiceData)
expect(servicesStore.createService).toHaveBeenCalledWith(mockServiceData)

// Testing error states
expect(getByRole('alert')).toHaveTextContent('Failed to fetch services')
```

## Testing Considerations
1. **Timer Management**: 
   - Use `vi.useFakeTimers()` for async operations
   - Clean up with `vi.useRealTimers()`
   - Run timers multiple times for nested async operations

2. **Store State**:
   - Mock both auth and services stores
   - Test authenticated and unauthenticated states
   - Verify store action calls

3. **Template Integration**:
   - Test template selection flow
   - Verify service creation from templates
   - Test template preview functionality 