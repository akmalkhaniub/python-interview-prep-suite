# Core Web Vitals & Page Speed

**Focus:** LCP, CLS, INP, optimization techniques, measuring impact

---

### Q1: The Three Core Web Vitals
**Question:** Google ranks pages based on Core Web Vitals. What are the three primary metrics, what do they measure, and what are their target thresholds?

**Expected Answer:**
1. **LCP (Largest Contentful Paint)**: Measures *loading performance*. Marks the time when the largest text or image block becomes visible. Target: **< 2.5 seconds**.
2. **CLS (Cumulative Layout Shift)**: Measures *visual stability*. Quantifies unexpected layout shifts (e.g., an ad loading late and pushing text down). Target: **< 0.1**.
3. **INP (Interaction to Next Paint)**: Measures *interactivity/responsiveness*. Replaced FID. Measures the latency of every tap, click, or keyboard interaction. Target: **< 200 milliseconds**.

---

### Q2: Optimizing Largest Contentful Paint (LCP)
**Question:** The hero image on our landing page is causing a poor LCP score. How do you fix this in a Next.js application?

**Expected Answer:**
- **Next.js Image Component**: Use `<Image />` instead of `<img>`. It automatically optimizes formats (WebP/AVIF) and resizes based on device.
- **Priority Attribute**: Add `priority={true}` to the LCP image. This tells Next.js to inject a `<link rel="preload">` tag in the `<head>`, ensuring the browser fetches it immediately rather than waiting to parse the DOM.
- **Lazy Loading**: Ensure `priority` is *only* on above-the-fold images. Next.js lazy-loads the rest by default.
- **Text over Image**: If possible, use CSS gradients or text for the LCP element instead of a heavy image. Text renders instantly.
- **Server Response Time**: If the HTML itself takes 2 seconds to generate (poor TTFB), LCP is doomed. Use SSG/ISR or edge caching to serve the initial HTML faster.

---

### Q3: Fixing Cumulative Layout Shift (CLS)
**Question:** We have a financial chart that fetches data via CSR. When the data loads, the chart expands and pushes the text below it down, causing CLS. How do you fix it?

**Expected Answer:**
- **Reserve Space**: The container holding the chart must have predefined dimensions (aspect ratio, explicit height/width, or CSS `min-height`) *before* the data loads.
- **Skeleton Loaders**: While the data is fetching, render a skeleton UI with the exact dimensions of the final chart.
- **Fonts**: Web fonts causing FOIT (Flash of Invisible Text) or FOUT (Flash of Unstyled Text) cause CLS when the fallback font swaps. Use Next.js `@next/font` (or `next/font/google`) which hosts fonts locally and uses `size-adjust` to prevent layout shifts.

---

### Q4: Improving Interaction to Next Paint (INP)
**Question:** A complex calculator page has a poor INP score. When a user drags a slider, the page freezes for 400ms before updating. How do you approach this?

**Expected Answer:**
- **Identify Main Thread Blocking**: The freezing means the JavaScript main thread is blocked by a heavy calculation.
- **Debouncing/Throttling**: If the slider triggers recalculations on every pixel move, debounce the input so the heavy math only runs when the user stops sliding.
- **React Transitions (`useTransition`)**: Mark the heavy state update as a non-urgent transition. This allows React to prioritize rendering the slider thumb moving immediately, and calculate the math in the background.
- **Web Workers**: If the financial math is truly massive (e.g., Monte Carlo simulations), offload the calculation to a Web Worker so it runs on a background thread, keeping the UI thread perfectly responsive.

---

### Q5: Measurement and Tools
**Question:** How do you track if your performance fixes actually work in the real world, not just on your local machine?

**Expected Answer:**
- **Lighthouse (Lab Data)**: Good for debugging locally, but it simulates a specific device/network. It does not reflect real users.
- **CrUX (Chrome UX Report) / Field Data**: This is what Google actually uses for rankings. Measured from real Chrome users.
- **Monitoring Tools**: Use Vercel Analytics, Google Search Console (Core Web Vitals report), or PageSpeed Insights to view 28-day rolling averages of field data.
- **Web Vitals Library**: I would import the `web-vitals` npm package into the app and send real-time metrics to our analytics provider (like Google Analytics or custom logging) to catch regressions immediately.
