# BrowsePage Test Documentation

## Overview
This document outlines the test coverage for the `BrowsePage.vue` component, which displays a list of service providers and allows users to view their services.

## Test Categories

### 1. Component Rendering
- **Loading State**
  - Verifies proper skeleton loading UI
  - Checks ARIA attributes for loading state
  - Tests loading indicator presentation

- **User List Display**
  - Validates correct rendering of active users
  - Confirms filtering of deleted users
  - Tests proper display of user names

- **Empty State**
  - Verifies "No Active Providers" message
  - Tests presence of releases link
  - Validates empty state styling

### 2. Accessibility
- **Heading Hierarchy**
  - Validates proper h1/h2 structure
  - Tests heading color classes
  - Ensures semantic HTML structure

- **ARIA Labels**
  - Tests button accessibility labels
  - Validates list/listitem roles
  - Checks region labeling

- **Loading State Accessibility**
  - Tests aria-busy attribute
  - Validates loading state announcements
  - Checks loading region labeling

### 3. Interactivity
- **Navigation**
  - Tests service profile navigation
  - Validates URL parameters
  - Checks router integration

- **Error Handling**
  - Tests API error scenarios
  - Validates error logging
  - Checks error state presentation

- **Event Logging**
  - Tests navigation event logging
  - Validates log message format
  - Checks metadata accuracy

## Test Implementation

### Setup
```typescript
// Mock dependencies
vi.mock('@/utils/logger');
const mockFetch = vi.fn();
global.fetch = mockFetch;

// Mock router configuration
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'Home',
      component: { template: '<div>Home</div>' }
    },
    {
      path: '/profile/:username',
      name: 'PublicProfile',
      component: { template: '<div>Profile</div>' }
    },
    {
      path: '/releases',
      name: 'Releases',
      component: { template: '<div>Releases</div>' }
    }
  ]
});

// Test data
const mockUsers = [
  { id: 1, username: 'john', display_name: 'John Doe' },
  { id: 2, username: 'jane', display_name: 'Jane Smith' },
  { id: 3, username: 'deleted', display_name: 'Deleted User' }
];
```

### Key Test Cases

#### Loading State
```typescript
it('shows loading skeleton initially', () => {
  render(BrowsePage, {
    global: { plugins: [router] }
  });

  expect(screen.getByRole('region')).toHaveAttribute('aria-label', 'Service providers list');
  expect(screen.getByLabelText('Loading service providers')).toBeInTheDocument();
  expect(screen.getAllByRole('presentation')).toHaveLength(3);
});
```

#### User Display
```typescript
it('displays users when data is loaded', async () => {
  mockFetch.mockResolvedValueOnce({
    ok: true,
    json: () => Promise.resolve(mockUsers)
  });

  render(BrowsePage, {
    global: { plugins: [router] }
  });

  const userList = await screen.findByRole('list');
  expect(userList).toHaveAttribute('aria-label', 'Available service providers');
  
  const userItems = screen.getAllByRole('listitem');
  expect(userItems).toHaveLength(2); // Excludes deleted user
  
  expect(screen.getByText('John Doe')).toBeInTheDocument();
  expect(screen.getByText('Jane Smith')).toBeInTheDocument();
});
```

#### Navigation
```typescript
it('navigates to user profile when view services is clicked', async () => {
  mockFetch.mockResolvedValueOnce({
    ok: true,
    json: () => Promise.resolve(mockUsers)
  });

  render(BrowsePage, {
    global: { plugins: [router] }
  });

  await screen.findByRole('list');
  const viewButton = screen.getByLabelText('View services for John Doe');
  await fireEvent.click(viewButton);

  await vi.waitFor(() => {
    expect(router.currentRoute.value.name).toBe('PublicProfile');
    expect(router.currentRoute.value.params.username).toBe('john');
  });
});
```

## Best Practices Implemented
1. Use of semantic queries (getByRole, getByLabelText)
2. Proper async/await handling
3. Comprehensive accessibility testing
4. Error case coverage
5. Mock cleanup between tests
6. Router state management
7. Proper ARIA attribute testing

## Coverage Areas
- [x] Component rendering states
- [x] Accessibility compliance
- [x] User interactions
- [x] Navigation behavior
- [x] Error handling
- [x] Event logging
- [x] Loading states
- [x] Empty states 