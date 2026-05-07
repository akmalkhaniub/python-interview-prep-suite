# Deep Dive: React Architecture & Best Practices

## 🏗️ Design Patterns for Scale
As a lead with 10+ years of experience, you should focus on patterns that reduce technical debt:
*   **Composition over Inheritance:** Using HOCs (deprecated mostly) -> Render Props -> **Custom Hooks**.
*   **Compound Components:** (e.g., `Tabs`, `Tab`, `Panel`) for flexible UI components.
*   **Feature-Sliced Design (FSD):** Organizing files by business domain rather than technical type (actions/reducers).

## ⚡ Performance Optimization
*   **Virtualization:** `react-window` or `react-virtualized` for long lists.
*   **Memoization:** Strategic use of `useMemo` and `useCallback`. Don't over-memoize; focus on expensive computations and preventing referential inequality in dependency arrays.
*   **Code Splitting:** `React.lazy` and Dynamic Imports for route-based and component-based splitting.

## 🛠️ Modern State Management
*   **Zustand:** Low boilerplate, external store pattern.
*   **TanStack Query (React Query):** For server state. Essential for lead roles to separate server cache from UI state.
*   **Context API:** For truly global, low-frequency updates (theming, auth).

## 💡 Interview Q&A
**Q: How do you handle a "Prop Drilling" issue in a legacy React app without introducing a heavy library like Redux?**
**A:** Use **Component Composition**. Pass components as props rather than data. If the hierarchy is too deep, use **Context API** for specific sub-trees. If the state is complex, consider **Zustand** as it's lightweight and easy to integrate incrementally.

**Q: React 18 introduced Concurrent Rendering. How does `useTransition` help with UI responsiveness?**
**A:** `useTransition` allows you to mark state updates as "non-urgent". React will keep the UI responsive while the heavy update is happening in the background. For example, typing in a search bar (urgent) vs filtering a large list (non-urgent).
