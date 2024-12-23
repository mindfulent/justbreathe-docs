# Toast Notification Testing Documentation

## Overview

The toast notification system has been thoroughly tested to ensure proper functionality, styling, and accessibility. The testing covers both the toast service (`toast.ts`) and the composable (`useToast.ts`).

## Test Files

1. `src/services/__tests__/toast.spec.ts`
2. `src/composables/__tests__/useToast.spec.ts`

## Test Coverage

### Toast Service Tests

The toast service tests cover:

1. **Base Functionality**
   - Correct toast rendering with default options
   - Proper handling of different toast types
   - Error handling and fallback behavior

2. **Styling**
   - Consistent use of `breathe-dark-secondary` background
   - Proper class application for different toast types
   - Responsive design classes

3. **Accessibility**
   - ARIA attributes for notifications
   - Screen reader compatibility
   - Keyboard interaction support

### Toast Composable Tests

The composable tests cover:

1. **API Methods**
   - `showToast` with string message and type
   - `showToast` with config object
   - Helper methods for each toast type

2. **Configuration**
   - Default options application
   - Custom options merging
   - Type safety and validation

3. **Special Toast Types**
   - Reward toasts with points
   - Badge toasts with custom info
   - Custom toasts with specific options

## Test Examples

### Testing Toast Service

```typescript
describe('toast service', () => {
  describe('showToast', () => {
    it('should call toast with correct base styling', () => {
      const service = useToastService();
      service.showToast({ message: 'Test message' });

      expect(mockToast).toHaveBeenCalledWith(
        expect.objectContaining({
          toastClassName: expect.stringContaining('!bg-breathe-dark-secondary')
        })
      );
    });

    it('should handle errors gracefully', () => {
      mockToast.mockImplementationOnce(() => { throw new Error('Test error'); });
      service.showToast({ message: 'Test message' });
      
      expect(mockToast).toHaveBeenCalledTimes(2); // Attempts fallback
    });
  });
});
```

### Testing Toast Composable

```typescript
describe('useToast', () => {
  describe('showToast', () => {
    it('should call showToast with string message and type', () => {
      const toast = useToast();
      toast.showToast('Test message', 'success');

      expect(mockShowToast).toHaveBeenCalledWith({
        message: 'Test message',
        type: 'success',
        timeout: 5000,
        closeOnClick: true,
        pauseOnHover: true,
        draggable: true,
        hideProgressBar: false
      });
    });
  });
});
```

## Testing Best Practices

1. **Mock Dependencies**
   - Mock `vue-toastification` to avoid actual toast rendering
   - Use `vi.mock()` for consistent mocking
   - Reset mocks between tests

2. **Test Error Cases**
   - Verify error handling behavior
   - Test fallback mechanisms
   - Ensure graceful degradation

3. **Verify Styling**
   - Test critical style classes
   - Verify responsive behavior
   - Check accessibility-related styles

4. **Type Safety**
   - Test with TypeScript strict mode
   - Verify type inference
   - Test invalid type combinations

## Running Tests

```bash
# Run all toast-related tests
npm run test src/services/__tests__/toast.spec.ts src/composables/__tests__/useToast.spec.ts

# Run with coverage
npm run test:coverage

# Run in watch mode during development
npm run test:watch
``` 