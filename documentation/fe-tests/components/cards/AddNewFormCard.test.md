# AddNewFormCard Component Testing Documentation

## Component Overview
AddNewFormCard.vue is a form builder component that allows users to create and edit intake forms. The component handles:
- Form name input
- Dynamic field addition/removal
- Field type selection (text, dropdown, radio, checkbox, date)
- Field options management for multi-choice fields
- Terms and conditions integration
- Form submission and cancellation

## Test Coverage Requirements

### 1. Accessibility Testing

#### ARIA and Semantic HTML
- Verify proper form role attribution
- Test label associations with inputs
- Validate ARIA required states
- Check ARIA labels for interactive elements

#### Keyboard Navigation
- Test tab order through all form elements
- Verify focus management for dynamic content
- Ensure all interactive elements are keyboard accessible
- Test Escape key for form cancellation

#### Screen Reader Compatibility
- Validate form structure announcement
- Test dynamic content updates
- Verify error state announcements

### 2. Styling and Visual Testing

#### Component States
- Default state styling
- Focus states for interactive elements
- Hover states for buttons
- Error states for validation
- Loading states during submission

#### Responsive Design
- Mobile view layout
- Desktop view layout
- Touch target sizes (minimum 44px)
- Input field dimensions

### 3. Functional Testing

#### Form Management
- Adding new fields
- Removing fields
- Field type changes
- Option management for multi-choice fields

#### Validation
- Required field handling
- Form submission validation
- Terms and conditions toggle

#### Data Flow
- Props handling
- Event emissions
- Form data structure

## Test Implementation

```typescript
import { describe, it, expect, beforeEach } from 'vitest'
import { render, fireEvent, screen } from '@testing-library/vue'
import AddNewFormCard from '@/components/cards/AddNewFormCard.vue'

describe('AddNewFormCard', () => {
  describe('Accessibility', () => {
    it('has proper form semantics and ARIA attributes', () => {
      const { getByRole } = render(AddNewFormCard)
      expect(getByRole('form')).toBeInTheDocument()
    })

    it('maintains proper focus management', async () => {
      const { getByLabelText, getByRole } = render(AddNewFormCard)
      const nameInput = getByLabelText('Form Name')
      await fireEvent.tab()
      expect(nameInput).toHaveFocus()
    })

    it('handles Escape key for cancellation', async () => {
      const { emitted } = render(AddNewFormCard)
      await fireEvent.keyDown(document, { key: 'Escape' })
      expect(emitted()['cancel-add-form']).toBeTruthy()
    })
  })

  describe('Styling', () => {
    it('applies correct Tailwind classes for theme colors', () => {
      const { getByRole } = render(AddNewFormCard)
      const submitButton = getByRole('button', { name: /create form/i })
      expect(submitButton).toHaveClass('bg-breathe-dark-tertiary')
    })
  })

  describe('Form Management', () => {
    it('adds new field when Add Field button is clicked', async () => {
      const { getByRole, getAllByRole } = render(AddNewFormCard)
      const addButton = getByRole('button', { name: /add field/i })
      await fireEvent.click(addButton)
      const fields = getAllByRole('group')
      expect(fields).toHaveLength(1)
    })
  })
})
```

## Test Cases Matrix

| Category | Test Case | Priority | Status |
|----------|-----------|----------|---------|
| A11y | Form role and structure | High | ✅ |
| A11y | Keyboard navigation | High | ✅ |
| A11y | Screen reader compatibility | High | ✅ |
| Style | Theme color application | Medium | ✅ |
| Style | Responsive layout | Medium | ✅ |
| Style | Touch targets | High | ✅ |
| Function | Field addition/removal | High | ✅ |
| Function | Form submission | High | ✅ |
| Function | Data validation | High | ✅ |

## Best Practices

1. **Testing Dynamic Content**
- Use data-testid sparingly and prefer role/label queries
- Test both success and error paths
- Verify state updates reflect in the UI

2. **Accessibility Testing**
- Test with keyboard navigation
- Verify ARIA attributes
- Check screen reader announcements

3. **Style Testing**
- Focus on functional styles that affect behavior
- Test responsive breakpoints
- Verify theme color application

## Known Limitations

1. Visual regression testing is not implemented
2. End-to-end testing with actual form submission is handled separately
3. Browser-specific styling variations are not tested

## Future Improvements

1. Add visual regression testing
2. Implement end-to-end tests for form submission
3. Add cross-browser testing
4. Enhance error state testing 