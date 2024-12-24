# BreathingCard Component Tests

The BreathingCard component is a key feature that provides an interactive breathing exercise experience. The test suite ensures both functional correctness and accessibility compliance.

## Test Structure

The tests are organized into four main categories:

1. Basic Rendering
2. Accessibility Features
3. Breathing Exercise Functionality
4. Event Emissions

## Test Coverage

### Basic Rendering Tests

Verifies that the component renders correctly with default props:
- Component title
- Description text
- Button text

```typescript
it('renders properly with default props', () => {
  expect(wrapper.find('h2').text()).toBe('4-7-8 Breathing Technique')
  expect(wrapper.find('p').text()).toBe('Test description')
  expect(wrapper.find('button').text()).toBe('Start Exercise')
})
```

### Accessibility Tests

Ensures the component is fully accessible to screen readers and assistive technologies:

1. **Initial ARIA State**
   - Region role and label
   - Timer role and label
   - Button ARIA labels

2. **Dynamic ARIA Updates**
   - Timer label updates during exercise phases
   - Phase announcements via live regions
   - Screen reader instructions

```typescript
it('has proper ARIA attributes in initial state', () => {
  const region = wrapper.find('[role="region"]')
  expect(region.attributes('aria-label')).toBe('Breathing exercise')
  // ...
})
```

### Breathing Exercise Functionality

Tests the core breathing exercise features:

1. **Exercise Start**
   - Proper initialization
   - Initial countdown value
   - Phase setting

2. **Complete Breathing Cycle**
   - Inhale phase (4 seconds)
   - Hold phase (7 seconds)
   - Exhale phase (8 seconds)
   - Cycle count increment

3. **Exercise Stop**
   - State reset
   - UI updates

### Event Emissions

Verifies proper event communication:

1. **Start Event**
   - Emitted when exercise begins

2. **Phase Change Events**
   - Inhale phase ('in')
   - Hold phase ('hold')
   - Exhale phase ('out')

## Test Setup

The test suite uses:
- Vitest for test running and assertions
- Vue Test Utils for component mounting
- Fake timers for time-based testing

```typescript
beforeEach(() => {
  vi.useFakeTimers()
  wrapper = mount(BreathingCard, {
    props: {
      title: '4-7-8 Breathing Technique',
      // ...other props
    }
  })
})
```

## Accessibility Considerations

The test suite specifically verifies:
1. Proper ARIA roles and labels
2. Live region updates for dynamic content
3. Screen reader instructions
4. Phase announcements
5. Timer state communication

## Running the Tests

```bash
# Run all component tests
npm run test:unit

# Run only BreathingCard tests
npm run test:unit BreathingCard
```

## Future Improvements

Potential areas for test expansion:
1. Different breathing patterns/timings
2. Error states
3. Mobile device interactions
4. Keyboard navigation testing
5. Additional screen reader announcement patterns 