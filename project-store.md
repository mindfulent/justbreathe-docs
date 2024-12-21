# Project store.js refactor into modules

## All the files that will need to be updated:

1. High-Priority Components (Direct Auth/User Dependencies):
   - [DONE] `components/GoogleAuthButton.vue` - Integrated with auth module and improved error handling
   - [DONE] `components/NavigationBar.vue` - Converted to composition API with modular store access
   - [DONE] `pages/LoginPage.vue` - Modernized with new auth module and improved UX
   - [DONE] `pages/SignupPage.vue` - Completely refactored with enhanced validation and UX
   - [DONE] `pages/GoogleAuthCallback.vue` - Modernized with improved error handling and UX

2. Notification-Related Components:
   - [TODO] `components/NotificationItem.vue`
   - [TODO] `components/NotificationList.vue`
   - [TODO] `components/NotificationModal.vue`
   - [TODO] `components/ToastContainer.vue`
   - [TODO] `components/ToastNotification.vue`
   - [TODO] `pages/NotificationSettingsPage.vue`
   - [TODO] `pages/NotificationTestPage.vue`

3. User Data Dependent Pages:
   - [TODO] `pages/SettingsPage.vue`
   - [TODO] `pages/PublicProfilePage.vue`
   - [TODO] `pages/ClientDetailsPage.vue`
   - [TODO] `pages/ClientsPage.vue`

4. Booking-Related Components:
   - [TODO] `components/AvailableTimeSlots.vue`
   - [TODO] `components/SelectADate.vue`
   - [TODO] `components/SelectATimezone.vue`
   - [TODO] `pages/BookServicePage.vue`
   - [TODO] `pages/BookingConfirmationPage.vue`
   - [TODO] `pages/BookingDetailsPage.vue`
   - [TODO] `pages/CreateBookingPage.vue`

5. Service Management:
   - [TODO] `components/ServiceTemplatesGrid.vue`
   - [TODO] `pages/ServicesPage.vue`

## Detailed Component Updates Required

### Authentication Components

1. `GoogleAuthButton.vue`:
   - No changes needed (already using composition API without direct store access)

2. `NavigationBar.vue`:
   - Line 170: Update store import
   - Lines 237-240: Remove isLoggedIn watch statement
   - Refactor to `<script setup>` style
   - Add explicit store module imports

3. `LoginPage.vue`:
   - Line 115: Update store import
   - Lines 127-171: Update Google login dispatch:
     ```javascript
     const authResult = await store.dispatch('auth/googleLogin', result.credential);
     ```
   - Lines 183-211: Update email login dispatch
   - Line 144 & 196: Update isLoggedIn getter usage

4. `SignupPage.vue`:
   - Line 133: Update store import
   - Lines 179-189: Update Google login dispatch
   - Consider moving Google initialization to a composable

### Notification Components

5. `NotificationList.vue`:
   - Line 214: Update store import
   - Line 222: Update notifications state access:
     ```javascript
     const activeNotifications = computed(() => 
       store.state.notifications.items.filter(notification => !notification.is_read)
     );
     ```
   - Line 227: Update readNotifications computation
   - Line 284: Update setNotifications dispatch
   - Line 305: Update markAsRead dispatch
   - Line 326: Update token watch for new store

6. `NotificationModal.vue`:
   - No direct store changes needed
   - Update event chain for markAsRead propagation

### Settings and User Management

7. `SettingsPage.vue`:
   - Line 150: Update user state access:
     ```javascript
     const user = computed(() => store.getters['user/user'] || {});
     const isLoggedIn = computed(() => store.getters['auth/isLoggedIn']);
     ```
   - Lines 164-165: Update user fetch dispatch
   - Line 249: Update username commit
   - Line 290: Update display name commit
   - Line 307: Update logout dispatch

### Utils and Composables

8. `useAuthGuard.js`:
   - Lines 9-10: Update computed properties:
     ```javascript
     const isLoggedIn = computed(() => store.getters['auth/isLoggedIn']);
     const user = computed(() => store.getters['user/user']);
     ```

9. `authErrorHandler.js`:
   - Line 13: Update logout dispatch:
     ```javascript
     await store.dispatch('auth/logout');
     ```

10. `api.js`:
    - Line 3: Ensure API_BASE import
    - Lines 18-22: Update token initialization:
      ```javascript
      import store from '@/store';
      const token = store.getters['auth/getToken'] || localStorage.getItem('token');
      ```
    - Line 31: Update token access
    - Line 64: Update token removal via store

### HomePage Components

11. `HomePage.vue`:
    - Line 192: Update store imports
    - Lines 195-197: Update computed properties
    - Lines 203-207: Update user data fetching
    - Update Google Calendar connection state

### Service Management

12. `ServicesPage.vue`:
    - Line 147: Update auth initialization
    - Line 286: Update token and user state handling
    - Lines 341-342: Update service creation with user ID
    - Lines 389-391: Update auth token handling

## General Recommendations

1. **Consistent Import Pattern**:
   ```javascript
   import { useStore } from 'vuex';
   import { computed } from 'vue';
   ```

2. **Computed Properties Pattern**:
   ```javascript
   const someState = computed(() => store.state.moduleName.property);
   const someGetter = computed(() => store.getters['moduleName/getterName']);
   ```

3. **Action Dispatch Pattern**:
   ```javascript
   await store.dispatch('moduleName/actionName', payload);
   ```

4. **Mutation Commit Pattern**:
   ```javascript
   store.commit('moduleName/mutationName', payload);
   ```

5. **Error Handling Pattern**:
   ```javascript
   try {
     await store.dispatch('moduleName/actionName');
   } catch (error) {
     if (isDebugMode()) {
       console.error('Error:', error);
     }
     showToast(error.message, 'error');
   }
   ```

## Next Steps

1. Begin with high-priority authentication components
2. Update utility functions and composables
3. Move to notification components
4. Update settings and user management
5. Finally, update service management components
6. Add comprehensive tests for new store interactions

Remember to test each component thoroughly after updates, especially checking:
- Authentication flows
- State persistence
- Error handling
- Component mounting/unmounting
- Route navigation guards

2. `NavigationBar.vue` Implementation Details:
   - Converted to <script setup> syntax
   - Implemented modular store access:
     ```javascript
     const isLoggedIn = computed(() => store.getters['auth/isLoggedIn'])
     const user = computed(() => store.getters['user/currentUser'])
     ```
   - Removed prop dependency for isLoggedIn
   - Maintained all existing functionality
   - Added proper TypeScript-like code organization

3. `GoogleAuthButton.vue` Implementation Details:
   - Added Vuex store integration with auth module
   - Implemented proper error handling and propagation
   - Added router integration for post-login navigation
   - Added error event emission for better error handling
   - Improved code organization and removed semicolons
   - Added scoped styling for consistent button width

4. `LoginPage.vue` Implementation Details:
   - Converted to <script setup> syntax
   - Integrated with new auth module using computed properties
   - Added loading states with proper UI feedback
   - Improved accessibility:
     - Added ARIA labels and descriptions
     - Added loading indicators
     - Improved keyboard navigation
   - Removed artificial delays in favor of proper state management
   - Added comprehensive error handling for both email and Google auth
   - Added proper disabled states during loading
   - Improved UX with loading spinner and disabled states

5. `SignupPage.vue` Implementation Details:
   - Complete rewrite using <script setup> syntax
   - Enhanced form validation with detailed error messages
   - Improved accessibility:
     - Added visible labels
     - Added error messages with ARIA attributes
     - Improved keyboard navigation
     - Added loading state announcements
   - Added comprehensive client-side validation
   - Integrated with new auth module for both email and Google signup
   - Added loading states with visual feedback
   - Improved error handling with field-level validation
   - Added reCAPTCHA integration
   - Enhanced UX with:
     - Proper form layout
     - Visual feedback
     - Disabled states
     - Loading indicators

6. `GoogleAuthCallback.vue` Implementation Details:
   - Converted to <script setup> syntax
   - Improved error handling with retry mechanism
   - Enhanced UX:
     - Added loading spinner
     - Better error messages
     - Retry button for failed attempts
     - Back to login option
   - Added proper state management:
     - Loading states
     - Error states
     - Retry count tracking
   - Improved accessibility:
     - Added ARIA roles
     - Screen reader support
     - Loading announcements
   - Added support for redirect URLs
   - Better error recovery with automatic retry
   - Improved debugging with better logging
