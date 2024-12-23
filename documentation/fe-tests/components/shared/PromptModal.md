# PromptModal Component Documentation

## Overview
The PromptModal is a reusable modal dialog component that follows accessibility best practices and implements the project's styling guidelines. It provides a consistent way to present prompts, confirmations, and alerts to users.

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