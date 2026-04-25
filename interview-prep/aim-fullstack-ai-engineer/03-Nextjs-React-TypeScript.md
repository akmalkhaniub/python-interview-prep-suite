# Frontend: Next.js, React & TypeScript
**Focus:** App Router, RSC, State Management, Tailwind, Web Performance

---

## Section A: Next.js Architecture & React

### Q1: Next.js App Router & React Server Components (RSC)
**Question:** Compare the Next.js App Router to the traditional Pages Router. What are the fundamental benefits of React Server Components (RSC)?

**Expected Answer:**
The traditional Next.js Pages Router relies on data fetching methods like `getServerSideProps` executed at the page level, passing massive JSON props down to a completely client-side rendered React tree. This results in large JavaScript bundles shipped to the browser.

The **App Router** represents a paradigm shift by defaulting to **React Server Components (RSC)**. RSCs execute exclusively on the server (Node.js edge/serverless) and *never* ship their JavaScript to the browser. They allow developers to write direct `async/await` database queries or internal API calls directly inside the React component itself. The server renders the component into a special serialized format and streams it to the client. This drastically reduces the client-side JavaScript bundle size, improving Time to Interactive (TTI) and SEO. Only highly interactive components (like a complex drag-and-drop rota builder) are explicitly marked with `"use client"`, ensuring we only ship JavaScript where user interactivity strictly demands it.

---

### Q2: State Management & Data Fetching
**Question:** How do you manage global state and data fetching in a modern Next.js App Router application?

**Expected Answer:**
With the advent of React Server Components, the need for heavy global state managers like Redux has drastically diminished. The majority of "state" is actually server data. In the App Router, I perform data fetching on the server within async RSCs and pass the data down as props. For mutations (like saving a payroll adjustment), I use **Server Actions**, which allow client components to call asynchronous server functions directly without manually writing intermediate API routes.

For managing complex *client-side* UI state (e.g., a multi-step modal wizard or a complex filtering UI), I prefer lightweight solutions like **Zustand** or simply relying on React's native Context API combined with `useReducer`. Furthermore, I aggressively utilize the URL as a state manager. Storing filter parameters or active tabs in the URL query string (`?status=active`) ensures the UI state is easily shareable, bookmarkable, and natively integrates with Next.js Server Components for seamless server-side re-rendering.

---

## Section B: TypeScript & Styling

### Q3: TypeScript in a Large React Codebase
**Question:** What are the primary benefits of using strictly typed TypeScript in a full-stack React and Node.js environment?

**Expected Answer:**
In a mission-critical platform like healthcare management, "undefined is not a function" is an unacceptable runtime error. TypeScript acts as a compile-time safety net, catching massive classes of bugs before code ever reaches the CI pipeline. By enforcing strict interfaces for our React component props, we ensure developers cannot inadvertently pass an `Invoice` object to a component expecting a `Payroll` object.

The most profound benefit emerges in a full-stack Next.js/Node.js monorepo: **End-to-End Type Safety**. We can define a single TypeScript interface for a `CarePlan` database model in the backend, use that exact same type for the Express/TRPC API response, and import it directly into the Next.js frontend to type the React props. If a backend engineer renames a database column from `client_id` to `patient_id`, the frontend React code immediately throws a compilation error, completely eliminating the API integration bugs that plague disjointed frontend/backend teams.

---

### Q4: Optimizing Performance with Tailwind CSS
**Question:** How do you optimize web performance and Core Web Vitals in a Next.js application using Tailwind CSS?

**Expected Answer:**
Tailwind CSS is inherently optimized for performance. Because it relies on utility classes, it completely eliminates the issue of growing, dead CSS code. During the build step, Tailwind's compiler scans the React components, identifies exactly which utility classes are used, and purges all unused CSS. This guarantees that the final CSS bundle shipped to the browser is microscopically small, often under 10kb, resulting in near-instant CSS parsing and exceptional First Contentful Paint (FCP) scores.

To further optimize Core Web Vitals (specifically Cumulative Layout Shift - CLS), I use Next.js's native `next/image` component to automatically serve correctly sized WebP images and prevent layout jumping. For Largest Contentful Paint (LCP), I utilize Next.js streaming with React Suspense boundaries. Instead of blocking the entire page render while waiting for a slow API call to fetch a complex rota, I wrap the Rota component in a `<Suspense fallback={<Skeleton />}>` boundary, allowing the critical shell of the application to render instantly.

---

### Q5: Responsive & Accessible Component Design
**Question:** Describe your approach to building responsive, accessible components for a care management platform.

**Expected Answer:**
A care management platform is often used by staff on legacy desktop monitors in offices, as well as on mobile devices in the field. Therefore, a "Mobile-First" approach is mandatory. Using Tailwind CSS, I start by defining the base layout for mobile screens and progressively enhance the layout using breakpoint prefixes (e.g., `md:grid-cols-2 lg:grid-cols-4`) for larger screens.

Accessibility (a11y) is equally critical, especially in healthcare tooling. I do not rely purely on standard HTML `<div>` tags; I rigorously employ semantic HTML5 tags (`<nav>`, `<main>`, `<article>`). For complex interactive components like dropdowns or modals, I utilize headless UI libraries (like Radix UI or Headless UI) that provide fully accessible, unstyled primitives out of the box, guaranteeing full keyboard navigation support, focus trapping, and proper ARIA attributes for screen readers without needing to reinvent complex accessibility logic from scratch.
