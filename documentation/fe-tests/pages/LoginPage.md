# LoginPage Component Testing

The LoginPage component demonstrates testing of form validation, authentication flows, loading states, and accessibility features for the login interface.

## Test Setup

```typescript
const mountComponent = () => {
  // Create a test pinia instance
  const pinia = createTestingPinia({
    createSpy: vi.fn
  })

  // Mock the auth store methods
  const store = useAuthStore(pinia)
  vi.spyOn(store, 'loginWithEmail').mockImplementation(async () => {
    await new Promise(resolve => setTimeout(resolve, 100))
    return { success: true }
  })

  // Mount with dependencies
  return mount(LoginPage, {
    global: {
      plugins: [pinia],
      components: {
        'CardComponent': mockCardComponent,
        'GoogleAuthButton': mockGoogleAuthButton
      },
      stubs: {
        'router-link': {
          template: '<a><slot></slot></a>',
          props: ['to']
        }
      }
    },
    attachTo: document.body // Important for focus management tests
  })
}
```

## Testing Categories

### Component Rendering
```typescript
describe('Component Rendering', () => {
  it('renders login form with proper structure', () => {
    const wrapper = mountComponent()
    expect(wrapper.find('main[role="main"]').exists()).toBe(true)
    expect(wrapper.find('#login-title').exists()).toBe(true)
    expect(wrapper.find('form').exists()).toBe(true)
  })

  it('applies proper color scheme from design system', () => {
    const wrapper = mountComponent()
    const emailLabel = wrapper.find('label[for="email"]')
    const submitButton = wrapper.find('button[type="submit"]')
    
    expect(emailLabel.classes()).toContain('text-breathe-dark-primary')
    expect(submitButton.classes()).toContain('bg-breathe-dark-tertiary')
  })

  it('shows loading state correctly', async () => {
    const wrapper = mountComponent()
    await wrapper.find('form').trigger('submit')
    await nextTick()
    
    const button = wrapper.find('button[type="submit"]')
    expect(button.text()).toContain('Signing in')
    expect(button.find('svg').exists()).toBe(true)
  })
})
```

### Accessibility Features
```typescript
describe('Accessibility', () => {
  it('has proper ARIA labels and roles', () => {
    const wrapper = mountComponent()
    expect(wrapper.find('main').attributes('role')).toBe('main')
    expect(wrapper.find('main').attributes('aria-labelledby')).toBe('login-title')
    expect(wrapper.find('[role="separator"]').exists()).toBe(true)
  })

  it('maintains proper focus management', async () => {
    const wrapper = mountComponent()
    const emailInput = wrapper.find('#email')
    const passwordInput = wrapper.find('#password')
    
    await (emailInput.element as HTMLInputElement).focus()
    await nextTick()
    expect(document.activeElement).toBe(emailInput.element)
    
    await (passwordInput.element as HTMLInputElement).focus()
    await nextTick()
    expect(document.activeElement).toBe(passwordInput.element)
  })

  it('has accessible form controls', () => {
    const wrapper = mountComponent()
    const emailInput = wrapper.find('#email')
    const passwordInput = wrapper.find('#password')
    
    expect(emailInput.attributes('aria-describedby')).toBe('email-error')
    expect(passwordInput.attributes('aria-describedby')).toBe('password-error')
  })
})
```

### Form Interaction
```typescript
describe('Form Interaction', () => {
  it('handles form submission', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    
    await wrapper.find('#email').setValue('test@example.com')
    await wrapper.find('#password').setValue('password123')
    await wrapper.find('form').trigger('submit')
    
    expect(authStore.loginWithEmail).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123'
    })
  })

  it('prevents multiple submissions while loading', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    
    await wrapper.find('form').trigger('submit')
    await nextTick()
    await wrapper.find('form').trigger('submit')
    
    expect(authStore.loginWithEmail).toHaveBeenCalledTimes(1)
  })
})
```

### Loading States
```typescript
describe('Loading States', () => {
  it('disables form controls during submission', async () => {
    const wrapper = mountComponent()
    await wrapper.find('form').trigger('submit')
    await nextTick()
    
    const emailInput = wrapper.find('#email')
    const passwordInput = wrapper.find('#password')
    const submitButton = wrapper.find('button[type="submit"]')
    
    expect(emailInput.attributes('disabled')).toBe('')
    expect(passwordInput.attributes('disabled')).toBe('')
    expect(submitButton.attributes('disabled')).toBe('')
  })

  it('shows loading indicator', async () => {
    const wrapper = mountComponent()
    await wrapper.find('form').trigger('submit')
    await nextTick()
    
    const loadingSpinner = wrapper.find('svg.animate-spin')
    const loadingText = wrapper.find('.sr-only')
    
    expect(loadingSpinner.exists()).toBe(true)
    expect(loadingText.text()).toBe('Signing in...')
  })
})
```

### Error Handling
```typescript
describe('Error Handling', () => {
  it('displays error toast on login failure', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    const mockToast = useToast()
    
    // Mock login failure
    vi.spyOn(authStore, 'loginWithEmail').mockResolvedValue({
      error: 'Invalid credentials'
    })
    
    await wrapper.find('form').trigger('submit')
    await nextTick()
    
    expect(mockToast.error).toHaveBeenCalledWith(
      'Invalid credentials',
      expect.any(Object)
    )
  })

  it('handles Google account error gracefully', async () => {
    const wrapper = mountComponent()
    const authStore = useAuthStore()
    const mockToast = useToast()
    
    // Mock Google account error
    vi.spyOn(authStore, 'loginWithEmail').mockResolvedValue({
      googleAccount: true
    })
    
    await wrapper.find('form').trigger('submit')
    await nextTick()
    
    expect(mockToast.error).toHaveBeenCalledWith(
      'Please use the "Sign in with Google" button for this account',
      expect.any(Object)
    )
  })
})