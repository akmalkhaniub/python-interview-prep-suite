# Technical Study Guide: Firebase & PWA Specialist (Dubai)

## 1. Firebase Firestore Mastery
- **Multi-tenancy:**
    - Single Database vs. Multiple Databases (Firestore supports multiple now).
    - Data modeling: Subcollections vs. Top-level collections with `tenantId`.
- **Security Rules:**
    - Role-based access: `allow read: if get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'manager'`.
    - Query-side security and performance.
- **Listeners:** Efficiently using `onSnapshot` without causing massive reads.

## 2. Firebase Cloud Functions
- **Triggers:** `onDocumentCreated`, `onDocumentUpdated`, `onCall` (HTTPS), and `onRequest`.
- **Scheduled Functions:** Using `functions.pubsub.schedule` for reports.
- **Admin SDK:** Managing user roles and sensitive Firestore operations from the backend.
- **Webhook Handling:** Validating HMAC signatures (e.g., Stripe-Signature or WhatsApp HMAC).

## 3. PWA (Progressive Web Apps)
- **Service Workers:**
    - Caching strategies: `CacheFirst`, `NetworkFirst`, `StaleWhileRevalidate`.
    - Workbox library usage.
- **Manifest:** Configuring `manifest.json` for "Add to Home Screen" support on iOS and Android.
- **FCM for PWA:** Handling push notifications in the background via the service worker.

## 4. Integrations & Payments
- **Payment Gateways:**
    - Redirect vs. Embedded checkout.
    - Handling asynchronous success via webhooks.
- **WhatsApp API:**
    - 360dialog setup and webhook structure.
    - Template messages vs. Session messages.
- **HMAC Validation:** Using `crypto` in Node.js to verify request authenticity.

## 5. Arabic RTL Layout (UAE Focus)
- **CSS:** Using `dir="rtl"` on the `html` tag.
- **Flexbox/Grid:** Understanding how `start` and `end` map in RTL vs. LTR.
- **Fonts:** Using Arabic-friendly Google Fonts like `Amiri`, `Lateef`, or `Cairo`.

## 6. GitHub & Deployment Discipline
- **Branching:** GitFlow or GitHub Flow (Production vs. Staging).
- **GitHub Actions:** Deploying to Firebase Hosting and Functions automatically.

## Resources to Review
- [Firebase Documentation: Firestore Multi-tenancy](https://firebase.google.com/docs/firestore/solutions/multitenancy)
- [Web.dev: Progressive Web Apps (PWA) Guide](https://web.dev/progressive-web-apps/)
- [360dialog: WhatsApp Business API Docs](https://docs.360dialog.com/)
- [Stripe: Webhook Signature Verification](https://stripe.com/docs/webhooks/signatures)
