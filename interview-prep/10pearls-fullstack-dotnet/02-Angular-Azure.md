# 02: Angular & Azure Frontend Integration

10Pearls prefers **Modern Angular** (16+) for building dynamic user experiences.

## 1. Modern Angular (v16+)
- **Standalone Components:** Eliminating the need for `NgModules` for simpler codebases.
- **Signals:** A new reactive primitive that tracks state changes more efficiently than Zone.js.
- **Deferrable Views (`@defer`):** Lazy loading parts of a component to improve initial load time.

## 2. RxJS & State Management
- **Observables:** Handling asynchronous data streams.
- **BehaviorSubject:** Holding the current state of data.
- **Operators:** `switchMap` (cancelling old requests), `catchError` (global error handling).

## 3. Azure Integration
- **Azure App Service:** Hosting the Angular frontend (static site or SSR).
- **Azure Static Web Apps (SWA):** Optimized hosting with built-in API integration.
- **Azure Functions:** Serverless backend for specific frontend triggers (e.g., sending an email).

## 4. Security
- **Azure Active Directory (AD):** Using MSAL.js for secure user login.
- **JWT (JSON Web Tokens):** Intercepting HTTP requests to add Authorization headers.

## 5. Potential Interview Questions
1. **Why are "Signals" being introduced in Angular?**
   - *Answer:* They provide a more predictable way to handle state updates. Unlike the traditional Change Detection (which checks everything), Signals tell Angular exactly *what* changed and *where*.
2. **What is the difference between `Promise` and `Observable`?**
   - *Answer:* A Promise handles a single event and is eager. An Observable can handle multiple events over time, is lazy (doesn't start until subscribed), and can be cancelled.
3. **How do you optimize an Angular app for performance?**
   - *Answer:* Use **OnPush** change detection, lazy load modules/routes, use `@defer` for non-critical UI, and optimize bundle size using the Angular CLI.
4. **How do you handle "CORS" issues between Angular and .NET API?**
   - *Answer:* Enable CORS in the .NET API's `Program.cs` by defining an allowed origin (e.g., the Angular dev server or production URL).
