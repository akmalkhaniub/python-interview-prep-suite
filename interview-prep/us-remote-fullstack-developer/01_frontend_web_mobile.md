# Deep Dive: Frontend & Mobile (React & React Native)

As a Full-Stack developer for a US company, you'll often be the bridge between design and implementation.

## 1. React Web Architecture
*   **Component Composition:** Building reusable UI components using props and children.
*   **Performance:** Avoiding unnecessary re-renders using `React.memo` and `useCallback`.
*   **Forms:** Handling complex forms with `React Hook Form` or `Formik`.

## 2. React Native (Cross-Platform)
*   **Native Modules:** When to use them and how the RN bridge works (or the new JSI architecture).
*   **Styling:** Differences between Web CSS and RN's Flexbox implementation.
*   **Debugging:** Using React DevTools and Flipper to inspect the hierarchy and state.

## 3. Shared Logic (Monorepos)
*   **The Idea:** Sharing 70-90% of business logic (API calls, state management) between Web and Mobile.
*   **Tools:** Using **NX** or **Turborepo** to manage a shared code folder.

## 4. Coding Exercise: Custom Hook
```javascript
// A hook to handle fetch with loading and error states
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

## Interview Questions
1.  "What are the main differences between React.js and React Native from a developer's perspective?"
2.  "How do you handle 'responsive' design in React Native without standard CSS media queries?"
3.  "Describe a time you had to optimize a React component for performance. What tools did you use?"
4.  "What is the 'Virtual DOM' and how does it improve web performance?"
