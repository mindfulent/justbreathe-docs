# PublicProfilePage Tests

## Overview
This document outlines the test coverage for the `PublicProfilePage` component, which displays a user's public profile and their available services.

## Test Categories

### Component Rendering
- ✅ Shows loading skeleton initially
- ✅ Renders profile information after loading
- ✅ Shows empty state when no services are available

### Accessibility
- ✅ Maintains proper heading hierarchy (h1 for name, h2 for sections)
- ✅ Provides proper ARIA labels for interactive elements
- ✅ Handles loading state accessibly with status role

### Interactivity
- ✅ Copies profile link to clipboard
- ✅ Handles clipboard copy failures gracefully

### Error Handling
- ✅ Handles API errors gracefully
- ✅ Redirects to home page on error
- ✅ Shows appropriate error toast messages

## Test Setup

### Mock Dependencies
```typescript
// Mock toast functionality
const mockShowToast = vi.fn()
vi.mock('@/composables/useToast', () => ({
  useToast: () => ({
    showToast: mockShowToast
  })
}))

// Mock API calls
vi.mock('@/utils/api', () => ({
  default: {
    get: vi.fn()
  }
}))
```

### Mock Data
```typescript
const mockProfile = {
  display_name: 'John Doe',
  username: 'johndoe',
  timezone: 'America/New_York',
  services: [
    {
      id: '1',
      name: 'Meditation Session',
      description: 'Guided meditation session',
      category: 'Wellness',
      options: [
        {
          id: 'opt1',
          duration: 60,
          fee: '75.00'
        }
      ]
    }
  ]
}
```

## Key Test Considerations

### Route Setup
- Component requires username parameter in route
- Tests use `/profile/:username` route
- Router is configured in test setup

### Async Testing
- Uses `waitFor` for async operations
- Handles API response timing
- Tests both success and error states

### Accessibility Testing
- Verifies ARIA labels
- Checks heading hierarchy
- Tests loading state accessibility

### Error States
- Tests API failure scenarios
- Verifies error message display
- Checks navigation on error

## Example Test

```typescript
it('renders profile information after loading', async () => {
  vi.mocked(api.get).mockResolvedValueOnce({ data: mockProfile })

  render(PublicProfilePage, {
    global: {
      plugins: [router]
    }
  })

  await waitFor(() => {
    expect(screen.getByRole('heading', { level: 1 })).toHaveTextContent('John Doe')
    expect(screen.getByText('America/New_York')).toBeInTheDocument()
  })
}) 