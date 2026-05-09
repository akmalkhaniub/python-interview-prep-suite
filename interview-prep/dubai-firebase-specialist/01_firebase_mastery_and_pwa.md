# Deep Dive: Firebase Mastery & PWA Reliability

This role requires "Expert level" knowledge of Firestore and the ability to ship production PWAs.

## 1. Advanced Firestore Modeling
*   **The Aggregator Pattern:** Since Firestore doesn't have `SUM()` or `COUNT()` over large collections, you must use Cloud Functions to update aggregate documents (e.g., `dailySales`) whenever a new order is placed.
*   **Sharding Counters:** If your restaurant has thousands of orders per minute, a single document counter will hit the 1 write/second limit. Use "distributed counters" to shard writes across multiple sub-documents.
*   **Security Rules for Multi-tenancy:**
    ```javascript
    match /restaurants/{restaurantId}/orders/{orderId} {
      allow read, write: if isEmployeeOf(restaurantId);
    }
    function isEmployeeOf(resId) {
      return get(/databases/$(database)/documents/employees/$(request.auth.uid)).data.restaurantId == resId;
    }
    ```

## 2. Cloud Functions for Business Logic
*   **Webhook Normalization:** When receiving different formats from Tap, Stripe, or WhatsApp, use a Cloud Function to "normalize" the data into your internal Firestore schema.
*   **Scheduled Reports:** Using `firebase-functions/v2/scheduler` to generate PDF reports and store them in Firebase Storage for the "Franchise HQ" to download.
*   **Auth Blocking Functions:** Using `beforeUserCreated` or `beforeUserSignedIn` triggers to enforce custom registration rules or assign default roles.

## 3. PWA (The "Offline-First" Kitchen App)
*   **Service Worker Lifecycle:** Understanding `install`, `activate`, and `fetch` events.
*   **Caching Strategy:**
    - **App Shell:** Cached via `CacheFirst` for instant loading.
    - **Order Data:** Handled by Firestore's built-in offline persistence (local IndexedDB).
*   **The Manifest:** Ensuring `theme_color` and `background_color` align with the brand for a "native-like" feel on the home screen.

## 4. FCM (Push Notifications)
*   **Web Push:** Requesting permissions and storing the token in Firestore under the user's document.
*   **Background Messages:** Using `onBackgroundMessage` in the service worker to show notifications when the browser tab is closed.

## Interview Questions
1.  "How do you prevent 'Race Conditions' when multiple staff members are updating the same order document in Firestore?"
2.  "Describe the difference between 'Stale-While-Revalidate' and 'Network-First' caching strategies in a PWA."
3.  "How do you handle Firestore Security Rules for a multi-role system (e.g., Manager can see everything, Kitchen can only see pending orders)?"
4.  "What is the cold start problem in Cloud Functions, and how do you minimize its impact on webhook processing?"
