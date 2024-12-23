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

### FooterBar.vue [DONE]

The FooterBar component demonstrates testing of responsive behavior, accessibility features, dynamic content transitions, and timed content rotation.

#### Test Setup

```typescript
const mountComponent = () => {
  return mountWithDeps(FooterBar, {
    global: {
      plugins: [
        createTestingPinia({
          initialState: {
            auth: {
              isAuthenticated: true
            }
          }
        })
      ]
    }
  })
}
```

#### Testing Categories

1. **Basic Rendering**
```typescript
it('renders correctly', async () => {
  const wrapper = mountComponent()
  expect(wrapper.exists()).toBe(true)
})
```

2. **Accessibility Features**
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles', () => {
    const wrapper = mountComponent()
    const footer = wrapper.find('footer')
    
    expect(footer.attributes('role')).toBe('contentinfo')
    expect(footer.attributes('aria-label')).toBe('Footer')
  })

  it('has accessible links with descriptive labels', () => {
    const wrapper = mountComponent()
    const links = wrapper.findAll('a, router-link')
    links.forEach(link => {
      expect(link.attributes('aria-label')).toBeTruthy()
    })
  })

  it('maintains proper focus management', () => {
    const wrapper = mountComponent()
    const links = wrapper.findAll('a, router-link')
    
    links.forEach(link => {
      expect(link.classes()).toContain('focus:ring-2')
      expect(link.classes()).toContain('focus:ring-breathe-light-primary')
    })
  })
})
```

3. **Styling and Visual Features**
```typescript
describe('Styling', () => {
  it('uses correct color scheme from design system', () => {
    const wrapper = mountComponent()
    const footer = wrapper.find('footer')
    
    expect(footer.classes()).toContain('bg-breathe-dark-secondary')
    expect(footer.classes()).toContain('text-white')
  })

  it('has proper hover states on interactive elements', () => {
    const wrapper = mountComponent()
    const links = wrapper.findAll('a, router-link')
    
    links.forEach(link => {
      if (link.classes().includes('bg-breathe-dark-tertiary')) {
        expect(link.classes()).toContain('hover:bg-opacity-90')
      } else {
        expect(link.classes()).toContain('hover:text-breathe-light-primary')
      }
    })
  })

  it('has proper touch target sizes', () => {
    const wrapper = mountComponent()
    const links = wrapper.findAll('a, router-link')
    
    links.forEach(link => {
      expect(link.classes()).toContain('min-h-[44px]')
      expect(link.classes()).toContain('min-w-[44px]')
    })
  })
})
```

4. **Content Rotation**
```typescript
describe('Content Rotation', () => {
  it('maintains horizontal layout for links', () => {
    const wrapper = mountComponent()
    const linkContainer = wrapper.find('[data-testid="footer-links"]')
    
    expect(linkContainer.classes()).toContain('flex-row')
    expect(linkContainer.classes()).toContain('gap-2')
  })

  it('alternates between navigation links and feedback link', async () => {
    const wrapper = mountComponent()
    
    // Initial state shows About/Privacy
    expect(wrapper.find('[data-testid="footer-links"]').exists()).toBe(true)
    expect(wrapper.find('[data-testid="feedback-link"]').exists()).toBe(false)
    
    // Wait for first toggle (10 seconds)
    await new Promise(resolve => setTimeout(resolve, 10000))
    await wrapper.vm.$nextTick()
    
    // Should show feedback link
    expect(wrapper.find('[data-testid="footer-links"]').exists()).toBe(false)
    expect(wrapper.find('[data-testid="feedback-link"]').exists()).toBe(true)
    
    // Wait for second toggle (5 seconds)
    await new Promise(resolve => setTimeout(resolve, 5000))
    await wrapper.vm.$nextTick()
    
    // Should show About/Privacy again
    expect(wrapper.find('[data-testid="footer-links"]').exists()).toBe(true)
    expect(wrapper.find('[data-testid="feedback-link"]').exists()).toBe(false)
  })
})
```

5. **Dynamic Content**
```typescript
describe('Dynamic Content', () => {
  it('displays API version when available', async () => {
    const wrapper = mountComponent()
    await wrapper.vm.$nextTick()
    
    const versionElement = wrapper.find('[data-testid="footer-version"]')
    expect(versionElement.exists()).toBe(true)
  })

  it('links to Signal group for feedback', async () => {
    const wrapper = mountComponent()
    await wrapper.vm.$nextTick()
    
    // Wait for feedback link to appear
    await new Promise(resolve => setTimeout(resolve, 10000))
    await wrapper.vm.$nextTick()
    
    const feedbackLink = wrapper.find('[data-testid="feedback-link"] a')
    expect(feedbackLink.attributes('href')).toBe('https://signal.group/#CjQKIMWDjGZvLYtcNr71f3KmEAnm2jgYhJkDKELj80G3SRGHEhC-vnv3XIss6smSZY5z9_PY')
    expect(feedbackLink.attributes('target')).toBe('_blank')
    expect(feedbackLink.attributes('rel')).toBe('noopener noreferrer')
  })
})
```

### HomePage.vue [DONE]

The HomePage component demonstrates testing of conditional rendering, state management, and accessibility features for both authenticated and unauthenticated views.

#### Test Setup

```typescript
const mountComponent = () => {
  return mountWithDeps(HomePage, {
    global: {
      plugins: [
        createTestingPinia({
          initialState: {
            auth: {
              isAuthenticated: false,
              currentUser: null,
              isLoading: false
            },
            notifications: {
              unreadCount: 0
            }
          }
        })
      ]
    }
  })
}
```

#### Testing Categories

1. **Conditional Rendering**
```typescript
describe('Conditional Rendering', () => {
  it('shows loading skeleton when loading', async () => {
    const wrapper = mountComponent()
    const store = useAuthStore()
    store.isLoading = true
    await wrapper.vm.$nextTick()
    
    expect(wrapper.find('[key="skeleton"]').exists()).toBe(true)
    expect(wrapper.find('[key="login"]').exists()).toBe(false)
    expect(wrapper.find('[key="content"]').exists()).toBe(false)
  })

  it('shows login view when not authenticated', async () => {
    const wrapper = mountComponent()
    await wrapper.vm.$nextTick()
    
    expect(wrapper.find('[key="login"]').exists()).toBe(true)
    expect(wrapper.find('[key="content"]').exists()).toBe(false)
  })

  it('shows content view when authenticated', async () => {
    const wrapper = mountComponent()
    const store = useAuthStore()
    store.isAuthenticated = true
    store.currentUser = { name: 'Test User' }
    await wrapper.vm.$nextTick()
    
    expect(wrapper.find('[key="content"]').exists()).toBe(true)
    expect(wrapper.find('[key="login"]').exists()).toBe(false)
  })
})
```

2. **Accessibility Features**
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels on interactive elements', () => {
    const wrapper = mountComponent()
    
    const buttons = wrapper.findAll('button, a')
    buttons.forEach(button => {
      expect(button.attributes('aria-label')).toBeTruthy()
    })
  })

  it('maintains proper heading hierarchy', () => {
    const wrapper = mountComponent()
    
    const h1 = wrapper.find('h1')
    const h2s = wrapper.findAll('h2')
    const h3s = wrapper.findAll('h3')
    
    expect(h1.exists()).toBe(true)
    expect(h2s.length).toBeGreaterThan(0)
    expect(h3s.length).toBeGreaterThan(0)
  })

  it('has proper focus management', () => {
    const wrapper = mountComponent()
    
    const focusableElements = wrapper.findAll('button, a, [tabindex]')
    focusableElements.forEach(element => {
      expect(element.classes()).toContain('focus:ring-2')
      expect(element.classes()).toContain('focus:outline-none')
    })
  })
})
```

3. **Styling and Visual Features**
```typescript
describe('Styling', () => {
  it('applies correct color scheme', () => {
    const wrapper = mountComponent()
    
    // Check headings
    const headings = wrapper.findAll('h1, h2, h3')
    headings.forEach(heading => {
      expect(heading.classes()).toContain('text-breathe-dark-primary')
    })
    
    // Check buttons
    const buttons = wrapper.findAll('button, a.bg-breathe-dark-tertiary')
    buttons.forEach(button => {
      expect(button.classes()).toContain('text-white')
      expect(button.classes()).toContain('hover:bg-breathe-dark-secondary')
    })
  })

  it('has proper backdrop blur effects', () => {
    const wrapper = mountComponent()
    
    const cards = wrapper.findAll('.bg-white\\/90')
    cards.forEach(card => {
      expect(card.classes()).toContain('backdrop-blur-sm')
    })
  })
})
```

4. **State Management**
```typescript
describe('State Management', () => {
  it('fetches notifications when authenticated', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    const notificationsStore = useNotificationsStore()
    
    // Mock notification fetch
    const fetchSpy = vi.spyOn(notificationsStore, 'fetchNotifications')
    
    // Login user
    authStore.isAuthenticated = true
    await wrapper.vm.$nextTick()
    
    expect(fetchSpy).toHaveBeenCalled()
  })

  it('displays unread notification count', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    const notificationsStore = useNotificationsStore()
    
    authStore.isAuthenticated = true
    notificationsStore.unreadCount = 5
    await wrapper.vm.$nextTick()
    
    const badge = wrapper.find('[aria-label="Unread notifications count"]')
    expect(badge.exists()).toBe(true)
    expect(badge.text()).toBe('5')
  })
})
```

5. **Component Integration**
```typescript
describe('Component Integration', () => {
  it('properly integrates SnowEffect component', () => {
    const wrapper = mountComponent()
    expect(wrapper.findComponent(SnowEffect).exists()).toBe(true)
  })

  it('properly integrates BackgroundVideo component', () => {
    const wrapper = mountComponent()
    const video = wrapper.findComponent(BackgroundVideo)
    expect(video.exists()).toBe(true)
    expect(video.props('show')).toBe(true)
  })

  it('properly integrates CardComponent for quick links', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    
    authStore.isAuthenticated = true
    await wrapper.vm.$nextTick()
    
    const card = wrapper.findComponent(CardComponent)
    expect(card.exists()).toBe(true)
    expect(card.props('title')).toBe('Quick Links')
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
