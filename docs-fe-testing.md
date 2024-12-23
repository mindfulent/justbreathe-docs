# Frontend Testing Documentation

## Component Testing Examples

### NavigationBar.vue [DONE]

The NavigationBar component serves as a reference implementation for our testing approach, demonstrating how to test both functional and accessibility features.

#### Test Setup

```typescript
const mountComponent = async () => {
  // Create a test router
  const router = createRouter({
    history: createWebHistory(),
    routes: [
      { path: '/', name: 'Home', component: {} },
      { path: '/login', name: 'Login', component: {} },
      { path: '/about', name: 'About', component: {} }
    ]
  })

  // Mount with dependencies and router
  const wrapper = mountWithDeps(NavigationBar, {
    global: {
      plugins: [router]
    }
  })

  await router.isReady()
  await wrapper.vm.$nextTick()
  return wrapper
}
```

#### Testing Categories

1. **Basic Rendering**
```typescript
it('renders correctly', async () => {
  const wrapper = await mountComponent()
  expect(wrapper.exists()).toBe(true)
})
```

2. **Accessibility Features**
```typescript
it('has proper ARIA attributes', async () => {
  const wrapper = await mountComponent()

  // Check main navigation role and label
  const nav = wrapper.find('nav')
  expect(nav.attributes('role')).toBe('navigation')
  expect(nav.attributes('aria-label')).toBe('Main navigation')

  // Check skip link for keyboard users
  const skipLink = wrapper.find('a[href="#main-content"]')
  expect(skipLink.exists()).toBe(true)
  expect(skipLink.classes()).toContain('sr-only')

  // Check mobile menu button
  const menuButton = wrapper.find('button[aria-controls="mobile-menu"]')
  expect(menuButton.attributes('aria-expanded')).toBe('false')
})
```

3. **Color Scheme and Styling**
```typescript
it('applies correct color scheme', async () => {
  const wrapper = await mountComponent()

  // Check navigation background
  const nav = wrapper.find('nav')
  expect(nav.classes()).toContain('bg-breathe-dark-secondary')

  // Check logo text
  const logo = wrapper.find('.logo-text')
  expect(logo.classes()).toContain('logo-text')

  // Check nav links color
  const navLinks = wrapper.findAll('a')
  navLinks.forEach(link => {
    if (!link.classes().includes('sr-only')) {
      expect(link.classes()).toContain('text-white')
      expect(link.classes()).toContain('hover:text-breathe-light-primary')
    }
  })
})
```

4. **Keyboard Navigation**
```typescript
it('handles keyboard navigation', async () => {
  const wrapper = await mountComponent()

  const navLinks = wrapper.findAll('a')
  navLinks.forEach(link => {
    if (!link.classes().includes('sr-only')) {
      expect(link.classes()).toContain('focus:outline-none')
      expect(link.classes()).toContain('focus:ring-2')
      expect(link.classes()).toContain('focus:ring-breathe-light-primary')
    }
  })
})
```

5. **Interactive Features**
```typescript
it('toggles mobile menu with proper ARIA states', async () => {
  const wrapper = await mountComponent()

  const menuButton = wrapper.find('button[aria-controls="mobile-menu"]')
  const mobileMenu = wrapper.find('#mobile-menu')

  // Initial state
  expect(menuButton.attributes('aria-expanded')).toBe('false')
  expect(mobileMenu.attributes('role')).toBe('menu')
  expect(mobileMenu.attributes('aria-orientation')).toBe('vertical')

  // After toggle
  await menuButton.trigger('click')
  await wrapper.vm.$nextTick()
  expect(menuButton.attributes('aria-expanded')).toBe('true')

  // Check menu items
  const menuItems = wrapper.findAll('[role="menuitem"]')
  menuItems.forEach(item => {
    expect(item.classes()).toContain('focus:ring-2')
    expect(item.classes()).toContain('focus:ring-breathe-light-primary')
  })
})
```

### Testing Best Practices

1. **Component Mounting**
   - Use `mountWithDeps` helper for consistent dependency injection
   - Wait for router and component updates using `await`
   - Provide necessary route configuration

2. **Accessibility Testing**
   - Verify ARIA roles and labels
   - Test keyboard navigation features
   - Check focus management
   - Ensure proper color contrast
   - Test screen reader compatibility

3. **Style Testing**
   - Focus on functional styles (hover, focus states)
   - Test responsive behavior
   - Verify color scheme implementation
   - Check transition classes

4. **Interactive Features**
   - Test user interactions (clicks, keyboard events)
   - Verify state changes
   - Check proper ARIA attribute updates
   - Test mobile responsiveness

5. **Test Organization**
   - Group related tests logically
   - Use descriptive test names
   - Keep tests focused and atomic
   - Add comments for complex assertions

### Common Testing Patterns

1. **Finding Elements**
```typescript
// By role
wrapper.find('[role="navigation"]')

// By ARIA attribute
wrapper.find('[aria-expanded="false"]')

// By class
wrapper.find('.logo-text')

// By test ID
wrapper.find('[data-testid="nav-container"]')
```

2. **Checking Classes**
```typescript
// Single class
expect(element.classes()).toContain('text-white')

// Multiple classes
expect(element.classes()).toContain('focus:outline-none')
expect(element.classes()).toContain('focus:ring-2')
```

3. **Testing Attributes**
```typescript
// ARIA attributes
expect(element.attributes('aria-label')).toBe('Main navigation')
expect(element.attributes('aria-expanded')).toBe('false')

// Regular attributes
expect(element.attributes('href')).toBe('#main-content')
```

4. **Testing Events**
```typescript
// Click events
await element.trigger('click')
await wrapper.vm.$nextTick()

// Keyboard events
await element.trigger('keyup.enter')
await wrapper.vm.$nextTick()
