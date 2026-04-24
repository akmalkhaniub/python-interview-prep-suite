# Frontend Development & React.js

**Focus:** React.js, component architecture, state management, performance optimization

---

### Q1: React Component Lifecycle & Hooks
**Question:** Explain the transition from class component lifecycles to React Hooks. How do you replicate `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` using `useEffect`?

**Expected Answer:**
- **Hooks paradigm**: Functional components use hooks to tap into React state and lifecycle features without writing a class.
- **`componentDidMount`**: Use `useEffect` with an empty dependency array `[]`. Runs once after initial render.
- **`componentDidUpdate`**: Use `useEffect` with specific variables in the dependency array `[prop, state]`. Runs when those variables change.
- **`componentWillUnmount`**: Return a cleanup function from within the `useEffect`. Useful for clearing intervals, removing event listeners, or aborting fetch requests.
- **Caveat**: `useEffect` runs *after* render, so the UI won't block.

```javascript
useEffect(() => {
  // componentDidMount logic (e.g., fetch data)
  const timer = setInterval(() => console.log('Tick'), 1000);

  // componentWillUnmount logic
  return () => clearInterval(timer);
}, []); // Empty array ensures it only runs once
```

---

### Q2: State Management in React
**Question:** How do you decide between local state (`useState`), Context API, and global state management libraries like Redux or Zustand?

**Expected Answer:**
- **Local State (`useState` / `useReducer`)**: For state isolated to a single component or passed down slightly (e.g., form inputs, toggle visibility).
- **Context API**: For global state that changes infrequently (e.g., theme, user authentication, language localization). Overusing it for frequently changing data causes unnecessary re-renders in all consumers.
- **Global State Libraries (Redux, Zustand, Recoil)**: For complex, frequently changing global state, heavy cross-component communication, or when you need robust DevTools (time-travel debugging).
- **Server State (React Query / SWR)**: For asynchronous data fetching, caching, synchronization, and updating server state. Separates UI state from server state.

---

### Q3: React Performance Optimization
**Question:** You notice your React application is rendering slowly and feeling sluggish. What steps do you take to identify and fix performance bottlenecks?

**Expected Answer:**
- **Identify**: Use React Developer Tools Profiler to record renders and find components taking too long or re-rendering unnecessarily.
- **Memoization (`React.memo`)**: Wrap functional components to prevent re-renders if props haven't changed. Use carefully, as the comparison itself has a cost.
- **`useMemo` & `useCallback`**: `useMemo` caches expensive calculations. `useCallback` caches function definitions to prevent breaking child component memoization when passed as props.
- **Code Splitting**: Use `React.lazy()` and `<Suspense>` to load components (like routes or heavy modals) only when needed, reducing the initial bundle size.
- **Virtualization**: For long lists (e.g., 10,000 table rows), use libraries like `react-window` or `react-virtualized` to only render items currently visible in the DOM.
- **Key Prop**: Ensure stable, unique `key` props when rendering lists to help React's reconciliation algorithm.

---

### Q4: Handling Asynchronous Operations
**Question:** What are the common issues when fetching data in a React component, and how do you handle them robustly?

**Expected Answer:**
- **Race conditions**: If component re-renders or unmounts before a fetch completes, state updates might happen on an unmounted component (memory leak warning).
- **Solution**: Use an `AbortController` to cancel the fetch request in the `useEffect` cleanup function.
- **Loading & Error States**: Always handle the UI for `loading` and `error` states to improve UX.
- **Modern Approach**: Use React Query or SWR. They handle caching, background fetching, retries, loading/error states, and prevent race conditions automatically.

```javascript
useEffect(() => {
  const controller = new AbortController();
  
  fetch('/api/data', { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });

  return () => controller.abort(); // Cleanup on unmount
}, []);
```

---

### Q5: React Context vs Prop Drilling
**Question:** What is prop drilling, why is it bad, and how do you fix it without using Redux?

**Expected Answer:**
- **Prop Drilling**: Passing data from a top-level component down multiple layers of intermediate components that don't need the data themselves, just to reach a deeply nested child.
- **Why it's bad**: Makes intermediate components harder to maintain and less reusable, as their props become cluttered with unrelated data.
- **Fix 1 - Component Composition**: Instead of passing data down, pass the child component itself as a prop (e.g., using `children` prop).
- **Fix 2 - Context API**: Create a Context provider at the top level and use the `useContext` hook in the deeply nested component to access the data directly.
