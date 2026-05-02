# 04 - Performance & Accessibility

## Performance Optimization

### 1. What are "Core Web Vitals"?
**Answer:**
Google's metrics for a healthy site:
- **LCP (Largest Contentful Paint):** Loading performance (should be <2.5s).
- **FID (First Input Delay):** Interactivity (should be <100ms).
- **CLS (Cumulative Layout Shift):** Visual stability (should be <0.1).

### 2. How do you optimize image loading?
**Answer:**
- **Lazy Loading:** `loading="lazy"` attribute.
- **Modern Formats:** WebP/AVIF.
- **Responsive Images:** `srcset` attribute for different screen sizes.
- **CDNs:** To serve images closer to the user.

### 3. What is "Tree Shaking"?
**Answer:**
A process during the build step (Webpack/Vite) that removes unused code from the final bundle, reducing size.

## Accessibility (a11y)

### 4. What is WCAG?
**Answer:**
**Web Content Accessibility Guidelines.** A set of standards to make web content accessible to people with disabilities (visual, auditory, physical, etc.).

### 5. Importance of "Semantic HTML"?
**Answer:**
Using tags like `<header>`, `<main>`, `<nav>`, `<button>` instead of just `<div>`s.
- **Why:** Screen readers use these tags to navigate the page, and they improve SEO.

### 6. How do you handle "Focus Management"?
**Answer:**
Ensuring the keyboard focus moves logically through the page (using `tabindex`) and that focus is handled correctly inside Modals/Dialogs (Focus Trapping).
- **ARIA Attributes:** Using `aria-label`, `aria-hidden`, etc., to provide additional context to assistive technologies.
