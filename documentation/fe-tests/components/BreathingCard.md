# BreathingCard Component Tests

The BreathingCard component is a key feature that provides an interactive breathing exercise experience. The test suite ensures both functional correctness and accessibility compliance, including audio-only mode for visually impaired users.

## Test Structure

The tests are organized into five main categories:

1. Basic Rendering
2. Accessibility Features
3. Audio Mode
4. Breathing Exercise Functionality
5. Event Emissions

## Test Coverage

### Basic Rendering Tests

Verifies that the component renders correctly with default props:
- Component title
- Description text
- Button text for both silent and audio modes
- Audio file import handling

```typescript
it('renders properly with default props', () => {
  expect(wrapper.find('h2').text()).toBe('4-7-8 Breathing Technique')
  expect(wrapper.find('p').text()).toBe('Test description')
  expect(wrapper.findAll('button')[0].text()).toBe('Start in Silent Mode')
  expect(wrapper.findAll('button')[1].text()).toBe('Start with Audio Only')
})
```

### Accessibility Tests

Ensures the component is fully accessible to screen readers and assistive technologies:

1. **Initial ARIA State**
   - Region role and label
   - Timer role and label
   - Button ARIA labels for both modes
   - Audio mode specific announcements

2. **Dynamic ARIA Updates**
   - Timer label updates during exercise phases
   - Phase announcements via live regions
   - Screen reader instructions
   - Audio mode state changes
   - Visualization hiding in audio-only mode

```typescript
it('has proper ARIA attributes in initial state', () => {
  const region = wrapper.find('[role="region"]')
  expect(region.attributes('aria-label')).toBe('Breathing exercise')
  
  const silentButton = wrapper.findAll('button')[0]
  expect(silentButton.attributes('aria-label')).toBe('Start breathing exercise in silent mode')
  
  const audioButton = wrapper.findAll('button')[1]
  expect(audioButton.attributes('aria-label')).toBe('Start breathing exercise with audio guidance')
})
```

### Audio Mode Tests

Tests the enhanced audio-only mode functionality:

1. **Audio Playback**
   - Proper initialization of audio with Vite asset imports
   - Continuous looping behavior
   - Cleanup on stop
   - Audio file path handling

2. **Visual Elements**
   - Complete hiding of timer and animations in audio mode
   - Maintain accessibility information
   - Screen reader announcements
   - Clear mode switching

3. **Audio Controls**
   - Start/stop functionality
   - State management
   - Error handling
   - Audio reset on stop

```typescript
describe('Audio Mode', () => {
  beforeEach(() => {
    // Mock audio file import
    vi.mock('@/assets/sounds/breathing.wav', () => ({
      default: 'mocked-audio-file-path'
    }))

    mockAudio = {
      play: vi.fn().mockResolvedValue(undefined),
      pause: vi.fn(),
      currentTime: 0,
      loop: false
    }
    vi.stubGlobal('Audio', vi.fn().mockImplementation(() => mockAudio))
  })

  it('starts audio playback in audio-only mode', async () => {
    const audioButton = wrapper.findAll('button')[1]
    await audioButton.trigger('click')
    
    expect(mockAudio.play).toHaveBeenCalled()
    expect(mockAudio.loop).toBe(true)
  })

  it('hides visualizations in audio-only mode', async () => {
    const audioButton = wrapper.findAll('button')[1]
    await audioButton.trigger('click')
    
    expect(wrapper.find('.animate-breath').exists()).toBe(false)
    expect(wrapper.find('[role="timer"]').exists()).toBe(false)
  })
})
```

### Breathing Exercise Functionality

Tests the core breathing exercise features:

1. **Exercise Start**
   - Proper initialization
   - Initial countdown value
   - Phase setting
   - Mode-specific behavior (silent vs audio)
   - Audio file loading

2. **Complete Breathing Cycle**
   - Inhale phase (4 seconds)
   - Hold phase (7 seconds)
   - Exhale phase (8 seconds)
   - Cycle count increment
   - Continuous audio in audio mode

3. **Exercise Stop**
   - State reset
   - UI updates
   - Audio cleanup and reset
   - Mode reset

### Event Emissions

Verifies proper event communication:

1. **Start Event**
   - Emitted when exercise begins
   - Includes mode information (silent/audio)

2. **Phase Change Events**
   - Inhale phase ('in')
   - Hold phase ('hold')
   - Exhale phase ('out')
   - Proper timing in both modes
   - Audio synchronization

## Test Setup

The test suite uses:
- Vitest for test running and assertions
- Vue Test Utils for component mounting
- Fake timers for time-based testing
- Audio API mocking for audio mode tests
- Vite asset handling mocks

```typescript
beforeEach(() => {
  // Mock audio file import
  vi.mock('@/assets/sounds/breathing.wav', () => ({
    default: 'mocked-audio-file-path'
  }))

  // Mock Audio API
  mockAudio = {
    play: vi.fn().mockResolvedValue(undefined),
    pause: vi.fn(),
    currentTime: 0,
    loop: false
  }
  vi.stubGlobal('Audio', vi.fn().mockImplementation(() => mockAudio))
  
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
6. Audio mode accessibility
7. Mode-specific announcements
8. Clear mode switching feedback

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
2. Error states for audio playback
3. Mobile device interactions
4. Keyboard navigation testing
5. Additional screen reader announcement patterns
6. Audio volume control testing
7. Network error handling for audio files
8. Different audio file formats support
9. Audio preloading strategies
10. Cross-browser audio compatibility 