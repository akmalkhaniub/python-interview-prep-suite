# 01 - React & Frontend Mastery

## React Fundamentals

### 1. What is the Virtual DOM and how does it improve performance?
**Answer:**
The Virtual DOM is a lightweight copy of the real DOM. When state changes:
1. React creates a new Virtual DOM tree.
2. It compares it with the previous tree (**Diffing**).
3. It calculates the minimum number of changes needed.
4. It applies those changes to the real DOM (**Reconciliation**).
- **Benefit:** Direct DOM manipulation is expensive; batching updates to the real DOM makes it significantly faster.

### 2. Difference between `useEffect` and `useLayoutEffect`?
**Answer:**
- **useEffect:** Runs **asynchronously** after the render is painted to the screen. Good for API calls, event listeners.
- **useLayoutEffect:** Runs **synchronously** after DOM mutations but *before* the paint. Good for measuring layout or animations where you want to prevent flickering.

### 3. How do you optimize a React component that re-renders too often?
**Answer:**
- **React.memo:** Prevents a component from re-rendering if its props haven't changed.
- **useMemo:** Memoizes expensive calculations.
- **useCallback:** Memoizes function definitions to prevent children from re-rendering.
- **Windowing/Virtualization:** Use `react-window` for long lists.
- **Lazy Loading:** Use `React.lazy` and `Suspense` for code splitting.

## State Management

### 4. When to use Context API vs. Redux/Zustand?
**Answer:**
- **Context API:** Good for low-frequency updates (e.g., Theme, Auth, Language). Not optimized for high-frequency updates because it triggers a re-render for all consumers.
- **Redux/Zustand:** Good for complex, high-frequency global state. They offer middleware (Redux Saga/Thunk) and better dev tools for debugging.

### 5. What are React Server Components (RSC)?
**Answer:**
Components that run exclusively on the server. 
- **Benefits:** Smaller bundle size (code stays on server), direct access to backend resources (DB/FileSystem), and improved initial load time.
- **Contrast:** Traditional components (Client Components) still run on the client for interactivity.
