# AvailableTimeSlots Component Tests

## Overview
The `AvailableTimeSlots` component is responsible for displaying and managing time slot selection in the booking flow. The test suite ensures proper rendering, time slot selection, accessibility, and error handling.

## Test Categories

### 1. Component Rendering
- Verifies proper structure and styling
- Tests loading state display
- Validates empty state messages
- Checks date and time slot display

### 2. Time Slot Selection
- Tests time slot selection events
- Validates selected time slot display
- Verifies confirmation button functionality

### 3. Time Formatting
- Tests correct time format display
- Validates timezone conversion
- Handles various time formats

### 4. Accessibility Features
- ARIA labels and roles
- Loading state announcements
- Alert messages for empty states
- Screen reader compatibility

### 5. Error Handling
- Invalid time slot format handling
- Graceful degradation
- Error logging

### 6. Logging
- Time slot selection logging
- Confirmation action logging
- Error state logging

## Test Implementation

### Component Setup
```typescript
const defaultProps = {
  serviceId: '95',
  optionId: '95',
  selectedDate: new Date('2024-12-25T12:00:00Z'),
  selectedTimezone: 'US/Pacific',
  availableTimeSlots: ['09:00', '10:00', '11:00', '14:00'],
  selectedTimeSlot: null,
  isLoadingTimeSlots: false,
  workingDays: ['1', '2', '3', '4', '5']
};
```

### Key Test Cases

#### 1. Rendering Tests
```typescript
it('renders with proper structure and styling', () => {
  const { getByText, getAllByRole } = render(AvailableTimeSlots, {
    props: defaultProps,
    global: {
      components: { CardComponent }
    }
  });

  expect(getByText('Available Time Slots')).toBeTruthy();
  const expectedDate = format(defaultProps.selectedDate, 'EEEE, MMMM d, yyyy');
  expect(getByText(expectedDate)).toBeTruthy();
  const timeSlotButtons = getAllByRole('button');
  expect(timeSlotButtons).toHaveLength(defaultProps.availableTimeSlots.length);
});
```

#### 2. Time Slot Selection Tests
```typescript
it('emits select-time-slot event when time slot is clicked', async () => {
  const { getAllByRole, emitted } = render(AvailableTimeSlots, {
    props: defaultProps,
    global: {
      components: { CardComponent }
    }
  });

  const timeSlotButtons = getAllByRole('button');
  await fireEvent.click(timeSlotButtons[0]);

  expect(emitted()['select-time-slot']).toBeTruthy();
  expect(emitted()['select-time-slot'][0]).toEqual(['09:00']);
});
```

#### 3. Accessibility Tests
```typescript
it('has proper ARIA labels and roles', () => {
  const { getAllByRole } = render(AvailableTimeSlots, {
    props: defaultProps,
    global: {
      components: { CardComponent }
    }
  });

  const timeSlotButtons = getAllByRole('button');
  timeSlotButtons.forEach(button => {
    expect(button).toHaveAttribute('aria-label');
  });
});
```

## Testing Best Practices

1. **Component Isolation**
   - Mock external dependencies (logger)
   - Use CardComponent in global components
   - Test component in isolation

2. **Accessibility Testing**
   - Test ARIA attributes
   - Verify screen reader text
   - Check loading state announcements

3. **Time and Timezone Handling**
   - Test timezone conversions
   - Validate time format display
   - Handle invalid time formats

4. **Error States**
   - Test invalid input handling
   - Verify error messages
   - Check error logging

5. **Event Handling**
   - Test event emissions
   - Verify event payloads
   - Check event timing

## Common Testing Patterns

### Testing Loading States
```typescript
it('shows loading state correctly', () => {
  const { getByLabelText } = render(AvailableTimeSlots, {
    props: {
      ...defaultProps,
      isLoadingTimeSlots: true
    },
    global: {
      components: { CardComponent }
    }
  });

  const loadingElement = getByLabelText('Loading available time slots');
  expect(loadingElement).toBeTruthy();
  expect(loadingElement.classList.contains('animate-pulse')).toBe(true);
});
```

### Testing Empty States
```typescript
it('shows message when no time slots available', () => {
  const { getByText } = render(AvailableTimeSlots, {
    props: {
      ...defaultProps,
      availableTimeSlots: []
    },
    global: {
      components: { CardComponent }
    }
  });

  expect(getByText('No available time slots for the selected date.')).toBeTruthy();
});
```

### Testing Error Handling
```typescript
it('handles invalid time slot format gracefully', () => {
  const { getAllByRole } = render(AvailableTimeSlots, {
    props: {
      ...defaultProps,
      availableTimeSlots: ['invalid', '10:00']
    },
    global: {
      components: { CardComponent }
    }
  });

  const timeSlotButtons = getAllByRole('button');
  expect(timeSlotButtons).toHaveLength(1); // Only valid time slot should be rendered
});
```

## Maintenance Notes

1. **Test Dependencies**
   - Keep CardComponent mock up to date
   - Update logger mock as needed
   - Maintain timezone test cases

2. **Test Coverage**
   - Maintain high coverage of core functionality
   - Add tests for new features
   - Update tests when component changes

3. **Accessibility Compliance**
   - Keep ARIA tests current
   - Update for new accessibility requirements
   - Test with screen readers periodically 