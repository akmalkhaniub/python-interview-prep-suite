# 🏦 Senior React Native Lead: Beyond Banking (SDK Integration)

> **Role Focus:** Integrating Travel, Shopping, and Lifestyle SDKs into a core Banking App.
> **Key Themes:** Abstraction Layers, Performance (SLA), Unified UX, Complex State.

---

## 🎯 JD Breakdown & Key Priorities

### 1. SDK & Vertical Integration (The "Beyond Banking" Problem)
*   **The Challenge:** Banks want to be "super apps." They need to integrate Partner SDKs (Expedia for Travel, Shopify for Shopping) without bloating the app or making the core banking features unstable.
*   **Keywords:** Abstraction layers, Partner upgrades, Native Modules, Dependency management.

### 2. Unified UI & Experience (One App, One Experience)
*   **The Challenge:** Third-party SDKs often come with their own "ugly" UI. Your job is to skin them or wrap them in Bank-branded components so the user doesn't feel like they left the bank app.
*   **Keywords:** Custom React Native components, Design System bridging, Accessibility.

### 3. Complex State & Flow Management
*   **The Challenge:** A travel booking isn't just one screen. It's: Search -> Select -> Ancillaries (Insurance/Seats) -> Checkout -> Bank OTP -> Confirmation. If the partner system hangs at "Ancillaries," how do you keep the user state safe?
*   **Keywords:** Multi-step journeys, Asynchronous partial states, Redux/Zustand state persistence.

### 4. Performance Engineering (Banking-Grade SLAs)
*   **The Challenge:** A slow travel module makes the *bank* look bad. You need to optimize the "Bridge," lazy-load partner assets, and ensure the SDK doesn't drain the battery.
*   **Keywords:** JS-to-Native bridge optimization, Asset loading, SDK initialization strategy.

---

## 🛠️ Portfolio Alignment (Framing Your Experience)

### 1. Linking "Procurement Intelligence Agent" (Python/AI) to React Native
*   **Why it matters:** Even though it's Python/AI, you solved **Complex State Management** and **Abstraction Layers** (MCP).
*   **The Talking Point:** "In my recent Agentic AI work, I implemented the Model Context Protocol (MCP) to create a clean abstraction between the orchestrator and various tools. I can apply this same 'Protocol-first' approach to React Native SDK integrations, ensuring partner upgrades don't break core banking services."

### 2. Linking "Kaggle Competition Dashboard" (React/Frontend)
*   **Why it matters:** It involves **Complex Data Visualisation** and **Real-time API Consumption**.
*   **The Talking Point:** "I led the refactoring of a monolithic dashboard into modular components. For the Bank app, I would implement a similar 'Micro-frontend' or 'Module-based' architecture in React Native, allowing travel and lifestyle verticals to evolve independently."

### 3. Native Experience (The Bridge)
*   **Requirement:** Optimization of JS-to-Native bridge.
*   **The Talking Point:** "I understand the overhead of the RN bridge. For high-performance modules, I prioritize JSI (JavaScript Interface) where possible or batch bridge calls to prevent frame drops during complex booking animations."

---

## ❓ Critical Interview Questions (Rehearse These)

### Section A: Architecture & SDKs
1.  **"How do you design a 'Wrapper' for a third-party SDK to ensure that a version update from the partner doesn't require a full app rewrite?"**
2.  **"What is your strategy for handling SDK initialization? Do you do it at app launch (slow start) or on-demand (lazy load)?"**

### Section B: Performance & SLAs
3.  **"A travel partner's SDK is causing memory leaks in our core banking app. Walk me through your debugging process."**
4.  **"How do you ensure accessibility (WCAG) compliance when the partner SDK provides its own 'closed' UI components?"**

### Section C: State & Complexity
5.  **"Talk about a time you managed a multi-step asynchronous flow. How did you handle 'Partial States' if the user kills the app mid-transaction?"**

---

## 🚀 Advanced Decisions to Discuss (The "Wow" Factor)

1.  **Dynamic Feature Flagging:** "I'd implement a server-driven UI or feature flagging (like LaunchDarkly) to remotely disable a partner vertical if their API goes down, without needing an App Store update."
2.  **Shadow SDK Initialisation:** "To meet banking SLAs, I'd initialize partner SDKs in a low-priority background thread only when the user enters the 'Beyond Banking' tab, ensuring the 'Account Summary' remains lightning fast."
3.  **Cross-Platform Consistency:** "I'd use a shared Design Token system (Figma to Code) to ensure that the Travel module matches the Bank's colors and typography down to the pixel."
