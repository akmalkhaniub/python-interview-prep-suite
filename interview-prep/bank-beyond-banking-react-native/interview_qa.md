# 💬 Interview Q&A: Bank React Native Lead

> **Focus:** Senior/Lead level answers for SDK integration, performance, and stakeholder management.

---

## 🏗️ Architecture & SDKs

**Q: "We have multiple partner verticals (Travel, Insurance, Shopping). How do you prevent one partner's code from crashing the entire banking app?"**
*   **Answer:** "I implement a **'Fault-Tolerance Layer'**. First, I wrap every partner-related entry point in a React **Error Boundary**. Second, I use an **Abstraction Interface**—I never call partner methods directly from our UI. If a partner SDK is unstable, I can 'toggle' that vertical off via a server-driven config without a re-deploy. Finally, I run partner SDKs in a **Sandbox** mode during development to audit their memory and thread usage before they hit the main app."

**Q: "Partner SDKs often come with their own UI. How do you bridge their UI with our Design System?"**
*   **Answer:** "It's a two-pronged approach. If the SDK is 'UI-less' (Headless), I build our own components using the Bank's design tokens. If the SDK is 'UI-heavy', I check for **Theming Support** in the SDK (passing colors/fonts). If that's unavailable, I wrap their views in a **UI-Adapter** where I inject our global styles or use 'Overlay' components (like custom headers/footers) to maintain brand continuity."

---

## ⚡ Performance

**Q: "How do you measure 'Banking-grade SLAs' for a mobile app?"**
*   **Answer:** "I focus on three pillars: **Time to Interactive (TTI)**, **Frame Stability (FPS)**, and **Failure Rate**. In a banking context, if a user clicks 'Travel' and it takes >3 seconds to load, that's an SLA breach. I use tools like **Flashlight** or **Performance Monitor** to track the impact of partner SDKs on the JS thread. I also implement **Performance Tracing** (via Firebase or Sentry) to measure the initialization time of each SDK in the wild."

---

## 🔄 State & Logic

**Q: "How do you manage state for a travel booking flow that involves 10+ screens and external API redirects?"**
*   **Answer:** "I use a **Finite State Machine (FSM)** pattern (using XState or a simple Reducer). This ensures that a user can only move from 'Search' to 'Select' if we have a valid session. For long-running flows, I use **Persistent State** (MMKV for speed). This is crucial for banking—if a user switches to their SMS app to get an OTP and the OS kills our app, they should be able to resume exactly where they left off when they return."

---

## 🤝 Leadership & Collaboration

**Q: "How do you work with backend teams who are building the middleware for these partner services?"**
*   **Answer:** "I advocate for **'Backend-for-Frontend' (BFF)**. Instead of the mobile app consuming raw partner APIs, our middleware should normalize the data. This reduces the logic we have to write in React Native. I also push for **Contract-First Development** using Swagger/OpenAPI, so I can mock the partner responses and start building the UI before the backend is even ready."

**Q: "You have 6-8 years of experience. How do you mentor junior developers in the team regarding native performance?"**
*   **Answer:** "I lead by example through **Rigorous Code Reviews**. I look for 'anti-patterns' like excessive re-renders in lists or heavy logic inside `useEffect`. I also host **'Performance Brown-Bags'** where we dive into the React Native architecture (the Bridge vs TurboModules) so the team understands *why* certain code is slow, not just *that* it's slow."
