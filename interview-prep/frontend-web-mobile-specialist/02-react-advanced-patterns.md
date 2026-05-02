# 02 - React Advanced Patterns

## Modern React

### 1. What is "Concurrent Mode" in React 18?
**Answer:**
A set of new features that allow React to be interruptible. It allows the UI to stay responsive even during heavy rendering tasks. Key features include `startTransition` and `useDeferredValue`.

### 2. Explain the "Render Props" pattern vs. "Custom Hooks."
**Answer:**
- **Render Props:** A technique for sharing code between components using a prop whose value is a function.
- **Custom Hooks:** The modern standard. They allow you to extract component logic into reusable functions.
- **Why Hooks?** They are cleaner, avoid "wrapper hell," and work better with TypeScript.

### 3. What is `React.Suspense`?
**Answer:**
A component that allows you to "wait" for something (like code or data) and show a fallback (like a spinner) until it's ready. In React 18, it works with Data Fetching frameworks (like Relay or Next.js) and for lazy-loading components.

## Performance Optimization

### 4. How do you implement "Code Splitting" in React?
**Answer:**
Using `React.lazy` and `import()`. This allows you to split the bundle into smaller chunks that are loaded on demand.
- **Example:** Only load the "Admin Dashboard" code when the user actually navigates to the `/admin` route.

### 5. What is the "Reconciliation" process?
**Answer:**
React's algorithm to update the DOM. It uses a **Virtual DOM** to calculate the difference (diffing) between the current state and the new state and applies only the necessary changes to the real DOM.

### 6. Importance of `key` prop in lists?
**Answer:**
Keys help React identify which items have changed, been added, or been removed. Using an index as a key is a **bad practice** because it can cause UI bugs during reordering or filtering; always use a unique ID from the data.
