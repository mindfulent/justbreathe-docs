# Just Breathe - Project Styling and Accessibility Guide

## Project Overview and Instructions

This implementation project has two main objectives:
1. Standardize the visual design across all 51 components and pages while preserving existing functionality
2. Ensure comprehensive accessibility compliance and testing for all components

For each file:
- Implement the defined color palette using Tailwind classes and CSS variables
- Apply accessibility standards and ARIA attributes as defined in the Accessibility Standards section
- Maintain WCAG 2.1 Level AA compliance
- Ensure all interactive elements retain their current behavior
- Test each component for both visual consistency and accessibility compliance
- Document accessibility features in component comments

Update the checklist status from [TODO] to [DONE] only after thorough testing confirms:
- No regression in component functionality
- Passing accessibility audit (see Testing Checklist)
- Screen reader compatibility verified
- Keyboard navigation tested

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

##### Core Components
1. [NEEDS UPDATED] NavigationBar.vue
2. [NEEDS UPDATED] FooterBar.vue
3. [TODO] TabComponent.vue
4. [TODO] ToastContent.vue

##### Interactive Components
5. [TODO] GoogleAuthButton.vue
6. [TODO] IntakeForm.vue
7. [TODO] ServiceTemplateCard.vue
8. [TODO] ServiceTemplatesGrid.vue
9. [TODO] SelectADate.vue
10. [TODO] SelectATimezone.vue
11. [TODO] AvailableTimeSlots.vue

##### Notification Components
12. [TODO] NotificationList.vue
13. [TODO] NotificationItem.vue

##### Effect Components
14. [TODO] SnowEffect.vue
15. [TODO] ConfettiEffect.vue
16. [TODO] BackgroundVideo.vue
17. [TODO] BadgeAwardModal.vue

##### Pages - Authentication & User Management
18. [TODO] LoginPage.vue
19. [TODO] SignupPage.vue
20. [TODO] ForgotPasswordPage.vue
21. [TODO] ResetPasswordPage.vue
22. [TODO] VerifyEmailPage.vue
23. [TODO] CheckYourEmailPage.vue
24. [TODO] GoogleAuthCallback.vue
25. [TODO] GoogleCalendarCallback.vue
26. [TODO] SettingsPage.vue

##### Pages - Core Features
27. [TODO] HomePage.vue
28. [TODO] AboutPage.vue
29. [TODO] PrivacyPolicyPage.vue
30. [TODO] TermsPage.vue
31. [TODO] FeedbackPage.vue

##### Pages - Service Management
32. [TODO] ServicesPage.vue
33. [TODO] ServiceDetailsPage.vue
34. [TODO] NewServicePage.vue
35. [TODO] BookServicePage.vue
36. [TODO] BookingConfirmationPage.vue
37. [TODO] BookingDetailsPage.vue
38. [TODO] CreateBookingPage.vue

##### Pages - Client Management
39. [TODO] ClientsPage.vue
40. [TODO] ClientDetailsPage.vue
41. [TODO] FormsPage.vue
42. [TODO] EmailTemplatesPage.vue

##### Pages - Notifications & Updates
43. [TODO] NotificationsPage.vue
44. [TODO] NotificationSettingsPage.vue
45. [TODO] NotificationTestPage.vue
46. [TODO] ReleasesPage.vue

##### Pages - Other
47. [TODO] BrowsePage.vue
48. [TODO] PublicProfilePage.vue
49. [TODO] NotFoundPage.vue
50. [TODO] DevAuthPage.vue (if needed for production)
51. [TODO] SnowTest.vue (if needed for production)

Total Files to Update: 51 files
Total Files Completed: 0 files
Last Updated: 2024-12-22 7:56 AM

#### Implementation Priority:
1. Core Components (#1-4)
2. Authentication Pages (#18-26)
3. Core Feature Pages (#27-31)
4. Service Management Components and Pages (#7-8, #32-38)
5. Client Management Components and Pages (#39-42)
6. Notification Components and Pages (#12-13, #43-46)
7. Effect Components (#14-17)
8. Remaining Pages and Components
