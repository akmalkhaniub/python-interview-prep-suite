# 03 - State Management & Data Flow

## State Strategies

### 1. When should state be "Local" vs. "Global"?
**Answer:**
- **Local:** If only one component (or its immediate children) needs the data (e.g., input values, toggle states). Use `useState`.
- **Global:** If multiple, distant components need the data (e.g., User Authentication, Shopping Cart, Theme). Use Redux, Zustand, or Context.

### 2. What is "Zustand" and why is it popular?
**Answer:**
A small, fast, and scalable state-management solution.
- **Why:** Much less boilerplate than Redux, doesn't require a Provider wrapper (no re-renders of the entire tree), and has a very simple hook-based API.

### 3. How do you handle "Server State" vs. "Client State"?
**Answer:**
- **Client State:** UI state (sidebar open, theme). Managed by Redux/Zustand.
- **Server State:** Data fetched from an API. Managed by libraries like **React Query** or **SWR**. These handle caching, refetching, and loading states automatically.

## Data Integration

### 4. REST vs. GraphQL for Frontend?
**Answer:**
- **REST:** Standard, but can lead to over-fetching (getting more data than needed).
- **GraphQL:** Allows the frontend to request exactly the fields it needs. Great for complex dashboards where one page might need data from multiple resources.

### 5. How do you handle "Optimistic UI" updates?
**Answer:**
Updating the UI immediately after a user action (e.g., "liking" a post) before the server confirms the success. If the server call fails, you "roll back" the state.
- **Benefit:** Makes the app feel instantaneous.

### 6. What is "Prop Drilling" and how to avoid it?
**Answer:**
Passing data through multiple layers of components that don't need it.
- **Fix:** Context API, State Management libraries, or Component Composition (passing components as props).
