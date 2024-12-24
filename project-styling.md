# Just Breathe - Styling, Accessibility and Frontend Testing Plan

## Completed Components
- [x] AddNewEmailTemplateCard.vue
- [x] AddNewServiceCard.vue
- [x] AddNewClientCard.vue
- [x] AddNewFormCard.vue
- [x] ServiceTemplateCard.vue
- [x] BadgeAwardModal.vue
- [x] LoginPage.vue
- [x] SignupPage.vue
- [x] ForgotPasswordPage.vue
- [x] VerifyEmailPage.vue
- [x] HomePage.vue
- [x] AboutPage.vue
- [x] ServicesPage.vue
- [x] ClientsPage.vue
- [x] EmailTemplatesPage.vue
- [x] PillTabs.vue

## Project Overview and Instructions

This implementation project has three main objectives:
1. Standardize the visual design across all 51 components and pages while preserving existing functionality
2. Ensure comprehensive accessibility compliance and testing for all components
3. Implement automated testing coverage for styling and accessibility features

For each file:
- Implement the defined color palette using Tailwind classes and CSS variables
- Apply accessibility standards and ARIA attributes as defined in the Accessibility Standards section
- Maintain WCAG 2.1 Level AA compliance
- Ensure all interactive elements retain their current behavior
- Test each component for both visual consistency and accessibility compliance
- Document accessibility features in component comments
- Create comprehensive unit tests covering styling and accessibility

Update the checklist status from [ ] to [x] only after thorough testing confirms:
- No regression in component functionality
- Passing accessibility audit (see Testing Checklist)
- Screen reader compatibility verified
- Keyboard navigation tested
- All unit tests passing

## Testing Requirements

### Unit Test Coverage
Each component must include tests for:

1. **Functional Styling**
   - Component transitions that affect user interaction (e.g., mobile menu animations)
   - Critical responsive behavior that affects functionality
   - Dynamic class applications (e.g., active states, loading states)

2. **Accessibility Features**
   - ARIA attributes presence and correctness
   - Keyboard navigation paths
   - Screen reader text content
   - Focus management
   - Color contrast compliance (via automated tools like axe-core)

3. **Interactive Behavior**
   - User interaction flows
   - State management
   - Event handling
   - Responsive adaptations

### Testing Best Practices

1. **Don't Test Static Styling**
   - Skip testing of static color applications
   - Skip testing of purely visual classes
   - Skip testing of basic layout classes
   
2. **Focus on Functional Changes**
   - Test class changes that affect user interaction
   - Test responsive behavior that changes functionality
   - Test dynamic style updates based on component state

3. **Prioritize User Experience**
   - Test transitions that provide user feedback
   - Test responsive adaptations that affect usability
   - Test state-based style changes that indicate component status

### Testing Tools
- Vitest as the test runner
- @testing-library/vue for component testing
- @testing-library/jest-dom for enhanced assertions
- @axe-core/vue for automated accessibility checks

### Test File Structure
```typescript
describe('Component Name', () => {
  describe('Component Rendering', () => {
    // Basic rendering tests
    // Responsive behavior tests
    // Style class applications
  })

  describe('Accessibility', () => {
    // ARIA attributes presence
    // Keyboard navigation
    // Screen reader text
    // Focus management
  })

  describe('Interactivity', () => {
    // User interactions
    // State changes
    // Event handling
  })

  describe('Responsive Behavior', () => {
    // Mobile vs desktop view
    // Component adaptations
  })
})
```

## Accessibility Standards

### 1. Semantic HTML and ARIA
- Use semantic HTML elements whenever possible (nav, main, article, section, etc.)
- Add appropriate ARIA labels and roles where semantic HTML is insufficient
- Implement proper heading hierarchy (h1 -> h6)
- Ensure form fields have associated labels
- Add aria-live regions for dynamic content

### 2. Keyboard Navigation
- Ensure all interactive elements are focusable
- Implement logical tab order
- Add visible focus indicators
- Provide keyboard shortcuts for common actions
- Ensure no keyboard traps

### 3. Screen Reader Compatibility
- Add descriptive alt text for images
- Implement aria-label for icons and decorative elements
- Provide context for screen readers through aria-describedby where needed
- Announce dynamic content changes
- Include skip links for main content

### 4. Interactive Elements
- Ensure sufficient touch targets (minimum 44x44px)
- Add hover and focus states
- Provide feedback for user actions
- Implement error handling with clear feedback
- Support both mouse and touch interactions

### Testing Checklist
For each component, verify:

#### Automated Testing
- Run aXe or similar accessibility testing tool
- Verify color contrast compliance
- Check HTML validity
- Test with ESLint a11y plugin

#### Manual Testing
- Test with VoiceOver/NVDA screen readers
- Verify keyboard navigation
- Check touch target sizes
- Test at 200% zoom
- Verify responsive behavior

#### Documentation
- Add accessibility features to component documentation
- Document any known limitations
- Include screen reader instructions if needed

## Color Palette and Text Contrast Guidelines

Our color palette is designed to create a calming, professional experience that aligns with our brand values of simplicity and effortless growth.

### Critical Text Contrast Rule

**IMPORTANT**: Always follow this fundamental rule for text contrast:
- Use white text (#FFFFFF) on dark background colors (dark-primary, dark-secondary, dark-tertiary)
- Use dark text (dark-primary) on light background colors (light-primary, light-secondary)

This rule is non-negotiable and ensures both accessibility compliance and visual consistency across the application. When in doubt, always err on the side of higher contrast.

### Dark Colors

| Name           | Hex Code | Usage                                    | Text Color |
|----------------|----------|------------------------------------------|------------|
| Primary Dark   | #162521  | Primary text, headings | White |
| Secondary Dark | #3a4a4a  | Footer background, secondary UI elements | White |
| Tertiary Dark  | #896978  | Call-to-action buttons, interactive elements | White |

### Light Colors

| Name            | Hex Code | Usage                                    | Text Color |
|-----------------|----------|------------------------------------------|------------|
| Primary Light   | #839791  | Icons, decorative elements              | Dark Primary |
| Secondary Light | #8a9290  | Input borders, subtle UI elements       | Dark Primary |

### Usage Guidelines

1. **Text Hierarchy**
   - Use Primary Dark (#162521) for main headings and important text on light backgrounds
   - Use white (#FFFFFF) for text on any dark background colors
   - Never use light text colors on light backgrounds

2. **Interactive Elements**
   - Use Tertiary Dark (#896978) with white text for primary call-to-action buttons
   - Use Primary Light (#839791) with dark text for secondary actions
   - Maintain consistent hover states within each color group

3. **Form Elements**
   - Use Secondary Light (#8a9290) for input borders and form element outlines
   - Always use dark text for input fields

### Accessibility

All color combinations in our palette have been chosen to maintain WCAG 2.1 compliance:
- Text colors maintain sufficient contrast with their background colors
- Interactive elements are clearly distinguishable from static content

### Technical Implementation 

#### 1. TailwindCSS Configuration

To make our colors easily configurable, we'll extend Tailwind's theme in `tailwind.config.js`:

```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        breathe: {
          // Dark Colors
          'dark-primary': '#162521',
          'dark-secondary': '#3a4a4a',
          'dark-tertiary': '#896978',
          // Light Colors
          'light-primary': '#839791',
          'light-secondary': '#8a9290',
        }
      }
    }
  }
}
```

#### 2. CSS Variables (as fallback)

For cases where we need dynamic color changes or non-Tailwind usage, define CSS variables in `src/assets/styles/variables.css`:

```css
:root {
  /* Dark Colors */
  --breathe-dark-primary: #162521;
  --breathe-dark-secondary: #3a4a4a;
  --breathe-dark-tertiary: #896978;
  
  /* Light Colors */
  --breathe-light-primary: #839791;
  --breathe-light-secondary: #8a9290;
}
```

#### 3. Usage Examples

In Vue components:

```vue
<!-- Using Tailwind Classes -->
<h1 class="text-breathe-dark-primary">Breathe Easy</h1>
<button class="bg-breathe-dark-tertiary text-white">Sign Up</button>
<div class="border-breathe-light-secondary">Input Field</div>

<!-- Using CSS Variables -->
<style scoped>
.custom-element {
  color: var(--breathe-dark-primary);
  border-color: var(--breathe-light-secondary);
}
</style>
```

#### 4. File Structure

```
frontend/
├── src/
│   ├── assets/
│   │   └── styles/
│   │       ├── variables.css    # CSS Variables
│   │       └── main.css        # Global styles
├── tailwind.config.js          # Tailwind configuration
```

#### 5. Implementation Steps

1. Update `tailwind.config.js` with our color palette
2. Create/update `variables.css` with CSS custom properties
3. Import `variables.css` in `main.css` or your app's entry point
4. Update existing components to use new color classes
5. Use CSS variables for any dynamic color requirements

This approach provides:
- Single source of truth for colors
- Easy global color updates
- Flexibility between utility classes and CSS variables
- TypeScript support through Tailwind's type system

#### 6. Frontend Components and Pages that need to be styled

[x] Denotes implementation completed

##### Core Components
1. [x] NavigationBar.vue
2. [x] FooterBar.vue
3. [x] PromptModal.vue
4. [x] PillTabs.vue (Replaces TabComponent.vue)
5. [x] ToastContent.vue (Removed - Replaced with native Vue-Toastification)
6. [x] services/toast.ts (Toast styling implementation)
7. [x] composables/useToast.ts (Toast composable)
8. [x] AddNewEmailTemplateCard.vue
9. [x] AddNewServiceCard.vue
10. [x] AddNewClientCard.vue
11. [x] AddNewFormCard.vue
12. [x] ServiceTemplateCard.vue
13. [x] BadgeAwardModal.vue

##### Interactive Components
14. [ ] GoogleAuthButton.vue
15. [ ] IntakeForm.vue
16. [ ] ServiceTemplatesGrid.vue
17. [ ] SelectADate.vue
18. [ ] SelectATimezone.vue
19. [ ] AvailableTimeSlots.vue
20. [ ] NotificationList.vue
21. [ ] NotificationItem.vue
22. [ ] SnowEffect.vue
23. [ ] ConfettiEffect.vue
24. [ ] BackgroundVideo.vue
25. [x] LoginPage.vue
26. [x] SignupPage.vue
27. [x] ForgotPasswordPage.vue
28. [x] ResetPasswordPage.vue
29. [x] VerifyEmailPage.vue
30. [x] CheckYourEmailPage.vue
31. [ ] GoogleAuthCallback.vue
32. [ ] GoogleCalendarCallback.vue
33. [ ] SettingsPage.vue
34. [x] HomePage.vue
35. [x] AboutPage.vue
36. [x] PrivacyPolicyPage.vue
37. [x] TermsPage.vue
38. [x] FeedbackPage.vue
39. [x] ServicesPage.vue
40. [ ] ServiceDetailsPage.vue
41. [ ] NewServicePage.vue
42. [ ] BookServicePage.vue
43. [ ] BookingConfirmationPage.vue
44. [ ] BookingDetailsPage.vue
45. [ ] CreateBookingPage.vue
46. [x] ClientsPage.vue
47. [ ] ClientDetailsPage.vue
48. [ ] FormsPage.vue
49. [x] EmailTemplatesPage.vue
50. [ ] NotificationsPage.vue
51. [ ] NotificationSettingsPage.vue
52. [ ] NotificationTestPage.vue
53. [ ] ReleasesPage.vue
54. [x] BrowsePage.vue
55. [ ] PublicProfilePage.vue
56. [x] NotFoundPage.vue
57. [ ] DevAuthPage.vue (if needed for production)
58. [ ] SnowTest.vue (if needed for production)

Total Files to Update: 51 files

#### Implementation Priority:
1. Core Components (#1-4)
2. Authentication Pages (#24-28)
3. Core Feature Pages (#33-37)
4. Service Management Components and Pages (#10-11, #38-41)
5. Client Management Components and Pages (#42-45)
6. Notification Components and Pages (#19-20, #46-48)
7. Effect Components (#21-23)
8. Remaining Pages and Components

## Documentation Workflow

### Frontend Testing Documentation
All frontend testing documentation should follow this workflow:

1. When completing items in this style guide:
   - First implement the required changes
   - Write and verify all tests
   - Document the testing approach in `docs/docs-fe-testing.md`

2. Documentation Priority:
   - `docs/docs-fe-testing.md` is the source of truth for all frontend testing documentation
   - Testing approaches, patterns, and examples should be documented there
   - This style guide references testing requirements but defers to `docs/docs-fe-testing.md` for implementation details

3. Documentation Update Process:
   - After completing a component's styling and testing
   - Add testing documentation to `docs/docs-fe-testing.md`
   - Update the component's status to [DONE] in this file

## Testing Standards

For detailed testing documentation, patterns, and examples, refer to `docs/docs-fe-testing.md`.

This section provides a high-level overview of testing requirements. For implementation details, always consult `docs/docs-fe-testing.md`.

### Testing Framework
- [DONE] Vitest as the primary testing framework
- [DONE] @testing-library/vue for component testing
- [DONE] @testing-library/jest-dom for enhanced DOM assertions

### Accessibility Testing
1. Manual Testing
   - Screen reader compatibility
   - Keyboard navigation
   - Focus management
   - Color contrast

2. Automated Testing
   - ARIA attributes and roles
   - Semantic HTML structure
   - Interactive element accessibility
   - Focus order and management

### Test Writing Standards
```typescript
import { describe, it, expect } from 'vitest'
import { render, fireEvent, screen } from '@testing-library/vue'

describe('ComponentName', () => {
  describe('Accessibility', () => {
    it('has proper ARIA labels and roles', () => {
      const { getByRole } = render(Component)
      const element = getByRole('button', { name: 'Submit form' })
      expect(element).toHaveAttribute('aria-label', 'Submit form')
    })

    it('maintains proper focus management', async () => {
      const { getByRole } = render(Component)
      const trigger = getByRole('button', { name: 'Open menu' })
      
      await fireEvent.click(trigger)
      expect(trigger).toHaveAttribute('aria-expanded', 'true')
    })
  })
})
```

### Testing Best Practices
1. Use Semantic Queries
   ```typescript
   // Preferred
   getByRole('button', { name: 'Submit' })
   getByLabelText('Email address')
   getByText('Welcome')

   // Avoid
   querySelector('.submit-button')
   querySelector('input[type="email"]')
   ```

2. Test Keyboard Navigation
   ```typescript
   await fireEvent.keyDown(element, { key: 'Tab' })
   await fireEvent.keyDown(element, { key: 'Enter' })
   ```

3. Test Focus Management
   ```typescript
   const trigger = getByRole('button')
   await fireEvent.click(trigger)
   expect(getByRole('dialog')).toHaveFocus()
   ```

4. Test ARIA States
   ```typescript
   expect(element).toHaveAttribute('aria-expanded', 'true')
   expect(element).toHaveAttribute('aria-hidden', 'false')
   ```

### Running Tests
```bash
# Run all tests
npm run test

# Run tests with coverage
npm run test:coverage

# Run tests in watch mode
npm run test:watch

# Run specific test file
npm run test path/to/file.spec.ts
```

## Appendix A: Frontend Testing Strategy

### Testing Framework and Setup
The project uses Vitest as the main testing framework, configured in `vitest.config.js/ts`. Key features include:
- JSDOM environment for DOM manipulation
- Global test setup files
- Code coverage reporting
- Test UI interface available

### Test Location Convention
- Tests should be placed in `__tests__` directories next to the code being tested
- Test files should follow the naming pattern: `*.{test,spec}.{js,mjs,cjs,ts,mts,cts,jsx,tsx}`

### Available Testing Commands
```bash
npm run test           # Run tests in watch mode
npm run test:coverage  # Run tests with coverage report
npm run test:ui        # Run tests with UI interface
npm run test:health    # Run API health check tests
```

### Testing Utilities and Mocks
The project provides several utilities and mocks in `src/test/setup.ts`:
- IntersectionObserver mock
- ResizeObserver mock
- matchMedia mock
- localStorage mock

Additional testing utilities in `src/test/test-utils.ts`:
- Pinia store setup helpers
- Test router creation
- Mock API responses

### Testing Libraries
- `@testing-library/vue` for component testing
- `@testing-library/jest-dom` for DOM assertions
- `@vue/test-utils` for Vue component testing
- `@pinia/testing` for Pinia store testing

### Example Test File Structures

1. Component Tests:
```typescript
// src/components/__tests__/MyComponent.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import MyComponent from '../MyComponent.vue'

describe('MyComponent', () => {
  it('renders correctly', () => {
    const wrapper = mount(MyComponent)
    expect(wrapper.exists()).toBe(true)
  })
})
```

2. Utility/Service Tests:
```typescript
// src/utils/__tests__/myUtil.spec.ts
import { describe, it, expect } from 'vitest'
import { myUtilFunction } from '../myUtil'

describe('myUtil', () => {
  it('performs expected operation', () => {
    expect(myUtilFunction()).toBe(expectedResult)
  })
})
```

3. Store Tests:
```typescript
// src/stores/__tests__/myStore.spec.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useMyStore } from '../myStore'

describe('MyStore', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })
  
  it('updates state correctly', () => {
    const store = useMyStore()
    store.someAction()
    expect(store.someState).toBe(expectedValue)
  })
})
```

### Best Practices
1. Keep test files close to the code they're testing
2. Use meaningful test descriptions
3. Test both success and error cases
4. Mock external dependencies
5. Use the provided test utilities for common operations
6. Include accessibility testing where relevant
7. Aim for good test coverage of critical paths

### Toast Styling Implementation [DONE]

The toast notification system has been implemented using Vue-Toastification with the following styling guidelines:

1. **Colors and Theme**
   - Uses `breathe-dark-secondary` (#3a4a4a) for all toast types
   - White text for optimal contrast
   - No drop shadows for a cleaner look
   - Hover effect with 90% opacity

2. **Responsive Design**
   - Mobile: Square corners for a full-width look
   - Desktop (≥640px): Rounded corners (rounded-lg)
   - Proper spacing above footer (40px margin)

3. **Toast Types**
   All toast types maintain consistent styling:
   - Success
   - Error
   - Info
   - Warning
   - Reward (with points display)
   - Badge (with wider width and name display)
   - Delete Success
   - Custom

4. **Accessibility Features**
   - High contrast text
   - Proper ARIA attributes
   - Keyboard interaction support
   - Screen reader compatibility

5. **Animation**
   - Smooth entry/exit animations
   - Custom cubic-bezier timing function
   - Proper transition handling

6. **Implementation Files**
   - `services/toast.ts`: Core styling and toast service with TypeScript types
   - `composables/useToast.ts`: Strongly-typed Vue composable
   - Global styles in `App.vue`

7. **Testing Coverage**
   - Unit tests for toast service
   - Unit tests for toast composable
   - Accessibility testing
   - Integration tests for all toast types
