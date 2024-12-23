# PillTabs Component

## Overview
The PillTabs component is a reusable navigation component that provides both desktop pill-style tabs and a mobile-friendly dropdown menu. It is designed to be accessible and responsive, with proper ARIA attributes and keyboard navigation support.

## Features
- Responsive design with desktop pills and mobile dropdown
- Accessible with proper ARIA labels and roles
- Keyboard navigation support
- Visual feedback for selected state
- TypeScript support with proper type definitions
- Event-based communication with parent components

## Props
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| tabs | Tab[] | Yes | - | Array of tab objects with id, name, and optional count |
| selectedTab | string | Yes | - | ID of the currently selected tab |
| ariaLabel | string | No | 'Navigation tabs' | ARIA label for the tab list |
| mobileLabel | string | No | 'Select tab' | Label for mobile dropdown |
| dropdownId | string | No | 'mobile-tab-select' | ID for mobile dropdown element |

## Events
| Name | Payload | Description |
|------|---------|-------------|
| update:selectedTab | string | Emitted when a tab is selected, with the new tab ID |

## Usage Example
```vue
<PillTabs
  v-model:selectedTab="selectedTab"
  :tabs="[
    { id: 'all', name: 'All' },
    { id: 'profile', name: 'Profile Settings' },
    { id: 'schedule', name: 'Schedule' }
  ]"
  aria-label="Settings navigation"
  mobile-label="Select settings section"
/>
```

## Test Coverage
The component is thoroughly tested with the following test cases:

### Desktop View Tests
- Renders all tabs as pills
- Marks the selected tab correctly
- Emits update:selectedTab event when tab is clicked
- Maintains proper ARIA attributes

### Mobile View Tests
- Renders a select element with all options
- Emits update:selectedTab event when selection changes
- Maintains proper accessibility in dropdown mode

## Implementation Notes
- Uses Tailwind CSS for styling
- Implements responsive design breakpoints
- Follows accessibility best practices
- Includes logging for debugging and monitoring

## Used In
- ServicesPage.vue (for service category filtering)
- SettingsPage.vue (for settings navigation)
- ServiceTemplatesGrid.vue (for template category filtering)

## Styling
The component uses Tailwind CSS classes for consistent styling:
- Pills use rounded-full for circular edges
- Consistent padding and spacing
- Color scheme follows the breathe design system
- Hover and focus states for better UX
- Mobile-first responsive design 