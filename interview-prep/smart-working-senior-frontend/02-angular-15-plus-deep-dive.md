# Deep Dive: Angular 15+ Deep Dive

## 🚀 Key Changes in v15 and beyond
*   **Standalone Components:** The biggest shift since v2. `standalone: true` removes the need for `NgModules`.
*   **Functional Interceptors:** Simpler, tree-shakable HTTP interceptors using functions instead of classes.
*   **Directive Composition API:** Allows you to host directives on a component to reuse logic (e.g., adding `CdkMenu` behavior to a custom button).
*   **Image Directive (`NgOptimizedImage`):** Built-in performance optimizations for LCP.

## 📡 Signals (v16/v17 focus)
Even if the JD mentions v15+, a senior developer must know **Signals**:
*   **What are they?** A reactive primitive that tracks dependencies and notifies consumers of changes.
*   **Signals vs RxJS:** RxJS is for asynchronous streams (events, HTTP). Signals are for state that changes over time and needs to be reflected in the UI.
*   **Benefits:** Fine-grained reactivity. Angular can update exactly where a signal is used rather than checking the whole component tree.

## 🏗️ Architectural Shifts
*   **Inject Function:** Replacing constructor injection with `inject(Service)`. Better for tree-shaking and functional programming patterns.
*   **Environment Providers:** Configuring global providers in `bootstrapApplication` instead of `AppModule`.

## 💡 Interview Q&A
**Q: What are the benefits of migrating to Standalone Components in an Angular 15 project?**
**A:** Better tree-shaking (components only pull in what they need), reduced boilerplate (no `NgModules`), and a more intuitive mental model for developers coming from React or Vue. It also makes component testing easier.

**Q: How does the new `NgOptimizedImage` directive improve Core Web Vitals?**
**A:** It enforces best practices: setting `width/height` to prevent Layout Shift (CLS), using `priority` for the Largest Contentful Paint (LCP) image, and automatically generating `srcset` for different screen sizes.

## 🛠️ Code Snippet: Standalone Component with Signal
```typescript
@Component({
  selector: 'app-user-profile',
  standalone: true,
  template: `<div>{{ user().name }}</div>`,
})
export class UserProfileComponent {
  // Using Signals for state
  user = signal({ name: 'John Doe' });

  updateName(newName: string) {
    this.user.set({ name: newName });
  }
}
```
