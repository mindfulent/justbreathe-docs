# FooterBar Component Testing

The FooterBar component demonstrates testing of responsive behavior, accessibility features, dynamic content transitions, and timed content rotation.

## Test Setup

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

## Testing Categories

### Basic Rendering
```typescript
it('renders correctly', async () => {
  const wrapper = mountComponent()
  expect(wrapper.exists()).toBe(true)
})
```

### Accessibility Features
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

### Styling and Visual Features
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

### Content Rotation
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

### Dynamic Content
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