# HomePage Component Testing

The HomePage component demonstrates testing of conditional rendering, state management, and accessibility features for both authenticated and unauthenticated views.

## Test Setup

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

## Testing Categories

### Conditional Rendering
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

### Accessibility Features
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

### Styling and Visual Features
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

### State Management
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

### Component Integration
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