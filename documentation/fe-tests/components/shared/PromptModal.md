# PromptModal Component Documentation

## Overview
The PromptModal is a reusable modal dialog component that follows accessibility best practices and implements the project's styling guidelines. It provides a consistent way to present prompts, confirmations, and alerts to users.

## Common Usage Patterns

### Deletion Confirmation Pattern
The PromptModal is commonly used for deletion confirmations throughout the application. This pattern has been standardized across various components including:
- Account deletion
- Email template deletion
- Form deletion
- Client deletion
- Service deletion

#### Implementation Pattern
```vue
<!-- Parent Component Template -->
<CardComponent>
  <button @click="showModal = true">Delete Item</button>
  <PromptModal
    v-model="showModal"
    title="Delete Item"
    message="Are you sure you want to delete this item? This action cannot be undone."
    confirm-text="Yes, Delete"
    cancel-text="Cancel"
    confirm-label="Confirm deletion"
    cancel-label="Cancel deletion"
    :show-cancel="true"
    @confirm="handleDelete"
    @cancel="cancelDelete"
  />
</CardComponent>

<!-- Parent Component Script -->
<script setup lang="ts">
const showModal = ref(false);
const isDeleting = ref(false);

const handleDelete = async () => {
  try {
    // Set deleting state to prevent further API calls
    isDeleting.value = true;
    
    // Small delay to ensure components unmount
    await new Promise(resolve => setTimeout(resolve, 100));
    
    // Perform deletion
    await deleteItem();
    
    // Show success message
    showToast('Item deleted successfully', 'success');
    
    // Clean up state if necessary
    // Redirect or update UI
  } catch (err) {
    isDeleting.value = false;
    showToast('Failed to delete item', 'error');
  }
};

const cancelDelete = () => {
  showModal.value = false;
};
</script>
```

#### Best Practices
1. **State Management**
   - Use `isDeleting` flag to prevent component API calls during deletion
   - Unmount components before starting deletion process
   - Clean up state after successful deletion

2. **Error Handling**
   - Reset deletion state on error
   - Show appropriate error messages
   - Maintain component state on failure

3. **User Experience**
   - Clear confirmation messages
   - Consistent button labeling
   - Immediate feedback via toast messages
   - Proper focus management

4. **API Call Prevention**
   - Unmount components before deletion to prevent unnecessary API calls
   - Add small delay to ensure unmounting completes
   - Clean up auth/user state in correct order

## Styling Implementation

### Color Scheme
- Background overlay: Black with 50% opacity (`bg-black bg-opacity-50`)
- Modal background: White (`bg-white`)
- Cancel button: Light secondary color (`bg-breathe-light-secondary`) with dark primary text (`text-breathe-dark-primary`)
- Confirm button: Dark tertiary color (`bg-breathe-dark-tertiary`) with white text (`text-white`)

### Interactive Elements
- Buttons have consistent hover states with opacity changes (`hover:bg-opacity-90`)
- Focus states use ring outlines matching button colors
- Minimum touch target sizes enforced (44x44px)
- Proper spacing between buttons (`space-x-4`)

### Typography
- Title: Extra large text with bold weight (`text-xl font-bold`)
- Message: Regular text with gray color (`text-gray-700`)

## Accessibility Features

### ARIA Attributes
- `role="dialog"` on the modal container
- `aria-modal="true"` to indicate modal behavior
- Dynamic `aria-labelledby` linked to modal title
- `role="document"` on the modal content
- Custom ARIA labels for buttons via props

### Keyboard Navigation
- Tab trap implementation within modal
- Escape key closes the modal
- Focus management:
  - Saves previously focused element
  - Auto-focuses cancel button (if present) or confirm button
  - Returns focus to previous element on close

### Screen Reader Considerations
- Semantic heading structure with `<h2>` for title
- Clear button labels with optional custom ARIA labels
- Proper focus management for modal lifecycle

## Testing Approach

### Component Rendering Tests
```typescript
describe('PromptModal Rendering', () => {
  it('renders when modelValue is true')
  it('does not render when modelValue is false')
  it('displays provided title and message')
  it('shows/hides cancel button based on showCancel prop')
  it('uses custom button text when provided')
})
```

### Accessibility Tests
```typescript
describe('PromptModal Accessibility', () => {
  it('has correct ARIA attributes')
  it('manages focus correctly')
  it('traps focus within modal')
  it('handles escape key press')
  it('returns focus on close')
})
```

### Interactive Behavior Tests
```typescript
describe('PromptModal Interactions', () => {
  it('emits confirm event on confirm button click')
  it('emits cancel event on cancel button click')
  it('emits update:modelValue on close')
  it('closes on overlay click')
})
```

### Test Coverage Goals
- 100% coverage of accessibility features
- 100% coverage of user interactions
- 100% coverage of prop variations
- 100% coverage of event emissions

## Props

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| modelValue | boolean | Yes | - | Controls modal visibility |
| title | string | No | - | Modal title text |
| message | string | Yes | - | Modal message content |
| confirmText | string | No | 'Confirm' | Confirm button text |
| cancelText | string | No | 'Cancel' | Cancel button text |
| confirmLabel | string | No | 'Confirm' | Confirm button ARIA label |
| cancelLabel | string | No | 'Cancel' | Cancel button ARIA label |
| showCancel | boolean | No | false | Whether to show cancel button |

## Events

| Event | Payload | Description |
|-------|---------|-------------|
| update:modelValue | boolean | Emitted when modal visibility changes |
| confirm | - | Emitted when confirm button clicked |
| cancel | - | Emitted when cancel button clicked |

## Usage Example

```vue
<PromptModal
  v-model="showModal"
  title="Confirm Action"
  message="Are you sure you want to proceed?"
  confirm-text="Yes, proceed"
  cancel-text="No, cancel"
  :show-cancel="true"
  @confirm="handleConfirm"
  @cancel="handleCancel"
/>
``` 