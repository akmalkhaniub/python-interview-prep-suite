# 03 - React Frontend & UX Flows

## Modern React

### 1. How do you manage "Complex State" in a React app?
**Answer:**
- **Context API:** For simple, app-wide state (Auth, Theme).
- **Redux/Zustand:** For complex, data-heavy states with many actions.
- **React Query/SWR:** For handling server-side data (caching, loading states, refetching).
- **Immutability:** Always treating state as immutable for predictable re-renders.

### 2. Best practices for "Performance" in React?
**Answer:**
- **Memoization:** Using `useMemo` and `useCallback` to avoid unnecessary recalculations.
- **Code Splitting:** Using `React.lazy` and `Suspense` to load components only when needed.
- **Virtualization:** Rendering only visible items in large lists (e.g., `react-window`).

### 3. Importance of "API Integration" patterns?
**Answer:**
Creating a dedicated "API Client" or hook layer to abstract fetch calls. This keeps components clean and makes it easy to switch or mock APIs.

## UX & Usability

### 4. What are "User Flows" and why do they matter?
**Answer:**
The path a user takes to complete a task. Designing these ensures that the product is intuitive and that there are no "Dead ends" or confusing steps.

### 5. Designing for "AI-Powered Features" (UX)?
**Answer:**
- **Feedback Loops:** Allowing users to rate AI outputs (Thumbs up/down).
- **Transparency:** Clearly marking AI-generated content.
- **Latency Management:** Using skeleton screens or progress indicators for long-running AI tasks.
- **Graceful Failure:** Providing fallbacks if the AI API is down or the output is poor.

### 6. Usability Principles for Engineers?
**Answer:**
- **Consistency:** Similar actions should look and behave the same way.
- **Affordance:** Buttons should look clickable.
- **Error Messages:** Should be helpful, not just technical codes.
- **Speed:** Perceived performance is as important as actual performance.
