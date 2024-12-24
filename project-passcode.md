# System Passcodes Technical Plan

## Introduction and Instructions

The System Passcodes feature implements a word-of-mouth access control system for two primary use cases / objectives:

1. [TODO] Objective 1: Pre-signup Verification
   - All new users must enter a valid passcode before creating an account
   - After entering email and password at SignupPage.vue, the user is redirected to PasscodePage.vue:
     - "We haven't created your account yet!"
     - "In order to create an account, please enter the passcode you received from a friend."
     - "Don't have a passcode? Want to get on the list for early access? Click here to save your username and password for later and be notified when we launch."
   - Replaces reCAPTCHA temporarily for simplified invite-only access during stealth mode (early 2025)
   - Applies to both email and Google sign-up flows - absolute requirement to sign up.
   - Passcode configurable via backend config.
   - Passcodes can be configured per-implementation. Pre-signup verification will have a different passcode than staging access.
   - Different defined passcodes can be used to identify different types of users at signup. We will have "prerelease" and "customerzero" passcodes that will ultimately award different badges upon entry.
   - **Dynamic Passcode Format**: All passcodes must be suffixed with the current day of the month (1-31)
     - Example: If base passcode is 'prerelease' and today is December 24th, the valid passcode would be 'prerelease24'
     - This adds a dynamic element to passcode security while remaining user-friendly

2. [TODO] Objective 2: Staging Environment Access Control
   - Future implementation
   - Will gate access to staging.justbreathe.com
   - Once entered correctly, a cookie persists for 365 days.
   - Same passcode validation mechanism and day-of-month requirement applies
   - Example: If today is the 24th, staging access requires 'boredatbutler24'

Security Requirements:
- Rate limiting: Max 3 incorrect attempts per IP address within 5 minutes
- IP address blocking for 5 minutes after exceeding attempt limit. User is redirected to BlockedPage.vue
- Full audit logging of all passcode attempts
- Dynamic day-of-month validation adds temporal security layer

## Schema for Passcode

### PasscodeAttempt Model

```python
class PasscodeAttempt(Base):
    __tablename__ = "passcode_attempts"

    id = Column(Integer, primary_key=True)
    ip_address = Column(String, nullable=False, index=True)
    passcode_attempted = Column(String, nullable=False)
    passcode_type = Column(String, nullable=False)  # 'prerelease', 'custzero', or 'staging'
    timestamp = Column(DateTime, nullable=False, default=datetime.utcnow)
    is_successful = Column(Boolean, nullable=False)
    user_agent = Column(String, nullable=True)
    referrer = Column(String, nullable=True)
    
    # For pre-signup verification
    email = Column(String, nullable=True)
    
    # For staging access
    is_staging_attempt = Column(Boolean, default=False)

    # Index for efficient rate limiting queries
    __table_args__ = (
        Index('idx_ip_timestamp', 'ip_address', 'timestamp'),
    )
```

### Configuration Schema

```python
# In backend/app/config.py

# System passcode configuration
SYSTEM_PASSCODES = {
    # Pre-release access passcode for early adopters
    'PRERELEASE_PASSCODE': {
        'value': os.getenv('PRERELEASE_PASSCODE', 'prerelease'),  # Will require day suffix, e.g., 'prerelease24'
        'badge': 'early_adopter',
        'description': 'Early access users who joined during pre-release phase'
    },
    
    # Customer Zero passcode for initial beta testers
    'CUSTZERO_PASSCODE': {
        'value': os.getenv('CUSTZERO_PASSCODE', 'custzero'),  # Will require day suffix, e.g., 'custzero24'
        'badge': 'customer_zero',
        'description': 'Our first beta testing customers'
    },
    
    # Staging environment access passcode
    'STAGING_PASSCODE': {
        'value': os.getenv('STAGING_PASSCODE', 'boredatbutler'),  # Will require day suffix, e.g., 'boredatbutler24'
        'description': 'Access to staging environment'
    },
    
    # Global rate limiting settings
    'rate_limit': {
        'max_attempts': 5,
        'block_duration_minutes': 5
    }
}
```

## Passcode Validation Logic

```python
def validate_passcode(attempted_passcode: str, passcode_type: str) -> bool:
    """
    Validates a passcode with day-of-month suffix
    Example: If base passcode is 'prerelease' and today is the 24th,
    valid passcode would be 'prerelease24'
    """
    # Get current day of month
    current_day = datetime.utcnow().day
    
    # Get base passcode from config
    base_passcode = SYSTEM_PASSCODES[passcode_type]['value']
    
    # Expected passcode is base + day of month
    expected_passcode = f"{base_passcode}{current_day}"
    
    return attempted_passcode == expected_passcode
```

## Frontend Implementation

The PasscodeForm component will include:
- Input field for passcode entry
- Helper text showing current day of month
- Clear error messaging for invalid attempts
- Visual feedback for remaining attempts before lockout

Example user interface text:
"Enter your passcode followed by today's date (e.g., if your passcode is 'prerelease' and today is the 24th, enter 'prerelease24')"

## Frontend Test Specifications

```
frontend/
  └── src/
      ├── pages/
      │   ├── __tests__/
      │   │   ├── [TODO] BlockedPage.spec.ts      # NEW: Page tests
      │   │   ├── [TODO] LoginPage.spec.ts        # NEW: Page tests
      │   │   ├── [TODO] PasscodePage.spec.ts     # NEW: Page tests
      │   │   ├── [TODO] SignupPage.spec.ts       # NEW: Page tests
      │   │   └── [TODO] StagingGatePage.spec.ts  # NEW: Page tests
      │   ├── [TODO] BlockedPage.vue             # NEW: Shows when IP is blocked
      │   ├── [TODO] LoginPage.vue               # UPDATE: Add Google auth redirect
      │   ├── [TODO] PasscodePage.vue            # NEW: Main passcode entry page
      │   ├── [TODO] SignupPage.vue              # UPDATE: Add passcode verification
      │   └── [TODO] StagingGatePage.vue         # NEW: Future staging access page
      ├── components/
      │   ├── __tests__/
      │   │   ├── [TODO] PasscodeForm.spec.ts    # NEW: Component tests
      │   │   └── [TODO] BlockedTimer.spec.ts    # NEW: Component tests
      │   ├── [TODO] PasscodeForm.vue            # NEW: Reusable passcode entry form
      │   └── [TODO] BlockedTimer.vue            # NEW: Reusable countdown timer
      ├── composables/
      │   ├── __tests__/
      │   │   └── [TODO] usePasscodeValidation.spec.ts  # NEW: Composable tests
      │   └── [TODO] usePasscodeValidation.ts    # NEW: Shared passcode validation logic
      ├── stores/
      │   ├── __tests__/
      │   │   └── [TODO] passcode.spec.ts        # NEW: Store tests
      │   └── [TODO] passcode.ts                 # NEW: Pinia store for passcode state
      ├── services/
      │   ├── __tests__/
      │   │   └── [TODO] passcode.spec.ts        # NEW: Service tests
      │   └── [TODO] passcode.ts                 # NEW: Passcode validation API client
      └── types/
          └── [TODO] passcode.ts                 # NEW: TypeScript interfaces
```

### Test Specifications Overview

1. `BlockedPage.spec.ts`
   - Test countdown timer functionality
   - Verify correct blocked duration display
   - Ensure proper error message rendering
   - Test accessibility requirements

2. `LoginPage.spec.ts`
   - Test Google auth flow redirects to PasscodePage
   - Verify proper state management during redirect
   - Test error handling scenarios

3. `PasscodePage.spec.ts`
   - Test passcode validation with day suffix
   - Verify attempt counter functionality
   - Test rate limiting UI feedback
   - Test both pre-signup and staging access flows
   - Verify proper routing on successful validation
   - Test accessibility of form inputs and error messages

4. `SignupPage.spec.ts`
   - Test redirect to PasscodePage before account creation
   - Verify email/password state preservation during redirect
   - Test Google signup flow with passcode requirement
   - Verify UI emphasis on email signup

5. `StagingGatePage.spec.ts`
   - Test passcode validation specific to staging
   - Verify cookie persistence (365 days)
   - Test rate limiting integration
   - Verify proper error handling

Each test file should include:
- Component mounting tests
- User interaction simulations
- Error state handling
- Accessibility compliance checks
- Route navigation tests
- State management verification

## Proposed File Structure Changes

```
frontend/
  └── src/
      ├── pages/
      │   ├── __tests__/
      │   │   ├── BlockedPage.spec.ts      # NEW: Page tests
      │   │   ├── LoginPage.spec.ts        # NEW: Page tests
      │   │   ├── PasscodePage.spec.ts     # NEW: Page tests
      │   │   ├── SignupPage.spec.ts       # NEW: Page tests
      │   │   └── StagingGatePage.spec.ts  # NEW: Page tests
      │   ├── BlockedPage.vue             # NEW: Shows when IP is blocked
      │   ├── LoginPage.vue               # UPDATE: Add Google auth redirect
      │   ├── PasscodePage.vue            # NEW: Main passcode entry page
      │   ├── SignupPage.vue              # UPDATE: Add passcode verification
      │   └── StagingGatePage.vue         # NEW: Future staging access page
      ├── components/
      │   ├── __tests__/
      │   │   ├── PasscodeForm.spec.ts    # NEW: Component tests
      │   │   └── BlockedTimer.spec.ts    # NEW: Component tests
      │   ├── PasscodeForm.vue            # NEW: Reusable passcode entry form
      │   └── BlockedTimer.vue            # NEW: Reusable countdown timer
      ├── composables/
      │   ├── __tests__/
      │   │   └── usePasscodeValidation.spec.ts  # NEW: Composable tests
      │   └── usePasscodeValidation.ts    # NEW: Shared passcode validation logic
      ├── stores/
      │   ├── __tests__/
      │   │   └── passcode.spec.ts        # NEW: Store tests
      │   └── passcode.ts                 # NEW: Pinia store for passcode state
      ├── services/
      │   ├── __tests__/
      │   │   └── passcode.spec.ts        # NEW: Service tests
      │   └── passcode.ts                 # NEW: Passcode validation API client
      └── types/
          └── passcode.ts                 # NEW: TypeScript interfaces
```

Key Additions:
1. Frontend:
   - Added reusable components (PasscodeForm, BlockedTimer)
   - Added composables for shared logic
   - Added Pinia store for state management
   - Added TypeScript types
   - Organized tests by component/page structure

2. Backend:
   - Added services layer for business logic
   - Added rate limiting utility
   - Added specific migration file for passcode tables

3. Documentation:
   - Added test documentation structure matching component organization
   - Separated documentation by component and page types

## Description of Each File

### Frontend Files

1. `BlockedPage.vue`
   - Shows when IP is blocked
   - Shows countdown timer until next attempt allowed
   - Clear error messaging

2. `LoginPage.vue`
   - If user tries logging in with Google, redirect to PasscodePage.vue befor account creation to confirm passcode first. 

3. `PasscodePage.vue`
   - Main passcode entry interface
   - Handles both pre-signup and staging access flows and future use cases
   - Shows attempt count and blocked status
   - Routes to appropriate next step on success

4. `SignupPage.vue`
   - User can enter email and password, but will be redirected to PasscodePage.vue first to confirm passcode.
   - Update UI to emphasize email signup and deprioritize Google signup option

5. `StagingGatePage.vue`
   - Future implementation
   - Will gate access to staging.justbreathe.com
   - Once entered correctly, a cookie persists for 365 days.
   - Same passcode validation mechanism and day-of-month requirement applies
   - Example: If today is the 24th, staging access requires 'boredatbutler24'

6. `passcode.ts`
   - API client for passcode validation
   - Handles rate limiting responses
   - Manages local storage for attempt tracking

### New Backend Files (excludes migration)

1. `models/passcode.py`
   - PasscodeAttempt model definition
   - Query methods for attempt history
   - Rate limiting helper methods

2. `schemas/passcode.py`
   - Business logic for passcode validation
    
3. `routers/passcode.py`
   - API endpoints for passcode validation

### Modified Files

1. `LoginPage.vue`
   - If user tries logging in with Google, redirect to PasscodePage.vue after authentication.

2. `SignupPage.vue`
   - Add passcode verification step after email and password are entered, before account creation.
   - If user tries signing up with Google, redirect to PasscodePage.vue, before account creation.
   - Update UI to emphasize email signup
   - Deprioritize Google signup option

Implementation Notes:
- Use Vue Router navigation guards to enforce passcode verification
- Implement proper error handling and user feedback
- Ensure accessibility standards are maintained
- Add comprehensive logging for security monitoring
- Consider adding admin dashboard for passcode attempt monitoring

### Test Documentation
All frontend component tests should include corresponding documentation files in the `@documentation` folder:

- Location: `@documentation/fe-tests/`
- File naming: `ComponentName.test.md` or `ComponentName.md`
- Structure:
  - Overview of component functionality
  - Test categories and coverage
  - Key test scenarios
  - Accessibility testing requirements
  - Mock data and setup
  - Common test patterns

Example locations:
- Components: `@documentation/fe-tests/components/`
- Pages: `@documentation/fe-tests/pages/`
- Cards: `@documentation/fe-tests/cards/`
