# Project Frontend Logger

## Overview
This project aims to standardize logging across the frontend application by replacing all console.log statements with our custom logger implementation. This will improve debugging, error tracking, and maintainability while providing consistent log formatting and control over log levels.

## Current Status
[DONE] Base logger implementation in `frontend/src/utils/logger.ts`
- Singleton pattern implementation
- Support for different log levels (error, warn, info, debug)
- Timestamp and module-based logging
- Metadata support
- Debug mode toggle based on VITE_DEBUG_MODE
- Environment-aware debug mode handling
- Helper methods for debug logging

## Environment-Specific Logging Configuration
[TODO] Implement environment-specific logging behavior:

### Local Development
- Debug Mode: Controlled by VITE_DEBUG_MODE in .env
- Default: VITE_DEBUG_MODE=true
- Developers can toggle logging by changing .env setting

### Staging Environment
- Debug Mode: Always enabled
- Set VITE_DEBUG_MODE=true in staging deployment
- All log levels visible for testing and verification

### Production Environment
- Debug Mode: Always disabled
- Set VITE_DEBUG_MODE=false in production deployment
- Only ERROR and WARN levels visible
- No DEBUG or sensitive information logged

## Goals
1. Replace all direct console.log/warn/error calls with logger
2. Add proper module context to all log calls
3. Implement appropriate log levels based on message importance
4. Add relevant metadata where applicable
5. Ensure proper error tracking with stack traces
6. Enable environment-specific logging behavior
7. [IMPORTANT] Remove ALL direct isDebugMode() checks from components
   - NEVER use isDebugMode() directly in components
   - Debug mode logic is handled internally by the logger
   - Always use logger.debug() which internally handles debug mode checks
   - Remove any existing isDebugMode imports and direct checks

## Implementation Strategy

### Key Principles
1. No direct debug mode checks
   - ❌ `if (isDebugMode()) { console.log() }`
   - ✅ `logger.debug()`

2. Centralized debug configuration
   - Debug mode is configured only in logger.ts
   - Components never need to know about debug mode
   - Logger handles all environment-specific behavior

3. Proper log levels
   - error: For errors that affect functionality
   - warn: For concerning but non-critical issues
   - info: For general information and successful operations
   - debug: For detailed debugging information (automatically handled by logger)

### Usage Examples

#### Basic Logging
```typescript
import { logger } from '@/utils/logger';

// Error logging with stack trace
try {
  // Some code that might throw
} catch (error) {
  logger.error(error, { module: 'AuthService' });
}

// Warning with metadata
logger.warn('API rate limit approaching', {
  module: 'APIClient',
  metadata: { remainingCalls: 50, resetTime: '2024-01-01T00:00:00Z' }
});

// Info logging
logger.info('User successfully authenticated', { 
  module: 'AuthService' 
});

// Debug logging
logger.debug('Form validation details', {
  module: 'IntakeForm',
  metadata: { fields: ['email', 'name'], isValid: true }
});
```

#### Module-Based Logging
```typescript
// In a Vue component
export default {
  name: 'BookingForm',
  setup() {
    const logOptions = { module: 'BookingForm' };
    
    const handleSubmit = async () => {
      logger.info('Submitting booking form', logOptions);
      try {
        // Form submission logic
        logger.debug('Form data', { 
          ...logOptions,
          metadata: { formData: /* form data */ }
        });
      } catch (error) {
        logger.error(error, logOptions);
      }
    };
  }
}
```

#### Conditional Debug Logging
```typescript
// Using debugIf helper
logger.debugIf(
  formData.isCompleted,
  'Form validation passed',
  { 
    module: 'ValidationService',
    metadata: { validationRules: ['required', 'email'] }
  }
);

// Using isDebugEnabled
if (logger.isDebugEnabled()) {
  // Perform expensive debug data gathering
  const debugData = gatherDebugData();
  logger.debug('Debug data gathered', {
    module: 'PerformanceMonitor',
    metadata: debugData
  });
}
```

#### Environment-Specific Behavior
```typescript
// Production: Only ERROR and WARN visible
logger.error('Payment processing failed', { module: 'PaymentService' }); // ✅ Shown
logger.warn('Retry attempt 2 of 3', { module: 'PaymentService' }); // ✅ Shown
logger.info('Payment initiated', { module: 'PaymentService' }); // ❌ Hidden
logger.debug('Payment payload', { module: 'PaymentService' }); // ❌ Hidden

// Staging: All levels visible
logger.error('Payment processing failed', { module: 'PaymentService' }); // ✅ Shown
logger.warn('Retry attempt 2 of 3', { module: 'PaymentService' }); // ✅ Shown
logger.info('Payment initiated', { module: 'PaymentService' }); // ✅ Shown
logger.debug('Payment payload', { module: 'PaymentService' }); // ✅ Shown

// Local: Controlled by VITE_DEBUG_MODE in logger.ts
logger.error('Payment processing failed', { module: 'PaymentService' }); // ✅ Always shown
logger.warn('Retry attempt 2 of 3', { module: 'PaymentService' }); // ✅ Always shown
logger.info('Payment initiated', { module: 'PaymentService' }); // ✅ Always shown
logger.debug('Payment payload', { module: 'PaymentService' }); // ✅ Shown if debug enabled
```

### Common Anti-patterns to Avoid
```typescript
// ❌ WRONG: Direct debug mode check
if (isDebugMode()) {
  console.log('Debug info');
}

// ❌ WRONG: Conditional debug logging
isDebugMode() && console.log('Debug info');

// ✅ CORRECT: Use logger.debug
logger.debug('Debug info', { module: 'MyComponent' });

// ❌ WRONG: Manual environment check
if (process.env.NODE_ENV === 'development') {
  console.log('Debug info');
}

// ✅ CORRECT: Let logger handle environment
logger.debug('Debug info', { module: 'MyComponent' });
```

### File Updates [TODO]
Files requiring updates:

#### Components
1. [DONE] NavigationBar.vue
2. [DONE] BadgeAwardModal.vue
3. [DONE] AvailableTimeSlots.vue
4. [DONE] ToastContent.vue
5. [DONE] NotificationItem.vue
6. [DONE] FooterBar.vue
7. [DONE] IntakeForm.vue
8. [DONE] ServiceTemplatesGrid.vue
9. [DONE] TabComponent.vue
10. [DONE] ConfettiEffect.vue
11. [DONE] BackgroundVideo.vue
12. [DONE] SelectATimezone.vue
13. [DONE] GoogleAuthButton.vue
14. [DONE] SelectADate.vue
15. [DONE] ServiceTemplateCard.vue
16. [DONE] NotificationList.vue
17. [DONE] ServiceInternalCard.vue
18. [DONE] ServicePublicCard.vue
19. [DONE] ServiceCard.vue
20. [DONE] EmailCard.vue
21. [DONE] ClientCard.vue
22. [DONE] BookingCard.vue

#### Utils
23. [DONE] api.ts
24. [DONE] analytics.ts
25. [DONE] googleAuth.js
26. [DONE] notifications.ts
27. [DONE] auth.ts
28. [DONE] authErrorHandler.js
29. [DONE] environment.js
30. [DONE] tokenStorage.ts
31. [DONE] termsAndConditions.js
32. [DONE] eventBus.js
33. [DONE] recaptcha.ts

#### Pages
34. [DONE] AboutPage.vue
35. [DONE] BookingConfirmationPage.vue
36. [DONE] BookingDetailsPage.vue
37. [DONE] BookServicePage.vue
38. [DONE] BrowsePage.vue
39. [DONE] CheckYourEmailPage.vue
40. [DONE] ClientDetailsPage.vue
41. [DONE] ClientsPage.vue
42. [DONE] CreateBookingPage.vue
43. [DONE] EmailTemplatesPage.vue
44. [DONE] FeedbackPage.vue
45. [DONE] ForgotPasswordPage.vue
46. [DONE] FormsPage.vue
47. [DONE] GoogleAuthCallback.vue
48. [DONE] GoogleCalendarCallback.vue
49. [DONE] HomePage.vue
50. [DONE] LoginPage.vue
51. [DONE] NewServicePage.vue
52. [DONE] NotFoundPage.vue
53. [DONE] NotificationSettingsPage.vue
54. [DONE] NotificationTestPage.vue
55. [DONE] NotificationsPage.vue
56. [DONE] PrivacyPolicyPage.vue
57. [DONE] PublicProfilePage.vue
58. [DONE] ReleasesPage.vue
59. [DONE] ResetPasswordPage.vue
60. [DONE] ServiceDetailsPage.vue
61. [DONE] ServicesPage.vue
62. [DONE] SettingsPage.vue
