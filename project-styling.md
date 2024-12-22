# Just Breathe - Project Styling Guide

## Color Palette

Our color palette is designed to create a calming, professional experience that aligns with our brand values of simplicity and effortless growth.

### Dark Colors

| Name           | Hex Code | Usage                                    |
|----------------|----------|------------------------------------------|
| Primary Dark   | #162521  | Primary text, headings (e.g., "Breathe Easy, Grow Effortlessly") |
| Secondary Dark | #3a4a4a  | Footer background, secondary UI elements |
| Tertiary Dark  | #896978  | Call-to-action buttons (e.g., "Sign Up") |

### Light Colors

| Name            | Hex Code | Usage                                    |
|-----------------|----------|------------------------------------------|
| Primary Light   | #839791  | Icons, decorative elements              |
| Secondary Light | #8a9290  | Input borders, subtle UI elements       |

### Usage Guidelines

1. **Text Hierarchy**
   - Use Primary Dark (#162521) for main headings and important text
   - Use Secondary Dark (#3a4a4a) for secondary text and UI elements

2. **Interactive Elements**
   - Use Tertiary Dark (#896978) for primary call-to-action buttons
   - Use Primary Light (#839791) for icons and interactive decorative elements

3. **Form Elements**
   - Use Secondary Light (#8a9290) for input borders and form element outlines

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