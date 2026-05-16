# 🧪 Technical Deep-Dive: React Native SDK & Performance Engineering

> This document focuses on the specific "Engineering Principles" mentioned in the JD: **Modular Architecture, Performance as a Feature, and Banking-grade SLAs.**

---

## 🏗️ 1. The "Abstraction Layer" Architecture

**Goal:** Replaceable, evolvable verticals (Travel, Shopping, etc.).

### The "Vertical Wrapper" Pattern
Instead of importing a Partner SDK directly into your screens, you create a `VerticalProvider`:

```javascript
// Example: TravelVerticalWrapper.js
import PartnerTravelSDK from 'partner-travel-sdk';

const TravelVertical = {
  initialize: (config) => {
    // Standardize config and init
    return PartnerTravelSDK.setup(config);
  },
  search: async (params) => {
    // Map Bank data types to Partner data types
    const results = await PartnerTravelSDK.findFlights(params);
    return results.map(item => normalizeTravelData(item));
  }
};
```

**Why this wins in an interview:** 
*   **Stability:** If the Bank swaps from Expedia to Booking.com, you only change the `normalizeTravelData` logic. The UI remains untouched.
*   **Version Safety:** You can run "Adapter Tests" to verify that a new SDK version still returns data in the format your Bank UI expects.

---

## ⚡ 2. Performance Engineering for Banking

**Goal:** JS-to-native bridge optimization and asset management.

### SDK Initialization Strategies
*   **Problem:** Initializing 5 SDKs (Travel, Food, Lifestyle, etc.) at app start will kill the "Time to Interactive" for the user's bank balance.
*   **Solution: "Just-In-Time" (JIT) Init.** 
    *   Pre-warm the SDK in the background when the user hovers over or clicks the "Beyond Banking" tab.
    *   Use `InteractionManager.runAfterInteractions` to ensure SDK setup doesn't block navigation animations.

### Bridge Optimization
*   **Batching:** Avoid "chatty" communication across the bridge. Instead of sending 10 small requests to the SDK, aggregate them into one `initializeVertical` payload.
*   **Binary Data:** If the SDK handles large assets (images/tickets), ensure they are handled natively and only "References" (URIs) are passed to the JS side.

---

## 🔄 3. Complex State: The "Recovery" Flow

**Goal:** Handling multi-step journeys and partial states.

### The "Transaction Sandbox"
For multi-step bookings, use a dedicated state slice that is **persisted** (e.g., via `redux-persist` or `mmkv`).

*   **Step 1:** User selects flight. (State: `PENDING_CHECKOUT`)
*   **Step 2:** User goes to Bank App to approve payment.
*   **Step 3:** User returns to Travel Vertical.
*   **Scenario:** If the app crashed during Step 2, the `rehydrate` logic detects the `PENDING_CHECKOUT` state and offers the user a "Resume Booking" flow.

---

## 🛡️ 4. Security & Reliability by Design

**Goal:** Banking-grade security.

### Data Leaking Prevention
*   **PII Masking:** Ensure that when passing data to a Partner SDK, you only send the minimum necessary info (e.g., a hashed `userId` instead of a real name).
*   **Secure Storage:** Never store Partner API keys or session tokens in `AsyncStorage`. Use `react-native-keychain` for Android Keystore/iOS Keychain integration.

---

## 📊 Performance Benchmarks (The "SLA" Talk)

Prepare to discuss these metrics:
1.  **Frame Rate (60 FPS):** Ensure partner modules don't cause "Jank" during transitions.
2.  **Bundle Size:** Mention use of `Dynamic Imports` or `Code-Splitting` (if using advanced bundlers like Re.Pack) to keep the core app size small.
3.  **Memory Footprint:** How you monitor the "Resident Set Size" (RSS) of the app when a heavy Partner SDK is active.
