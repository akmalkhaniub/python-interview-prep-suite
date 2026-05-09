# Deep Dive: React Native Expo & Flutter Web Nuances

This role requires versatility across the two leading cross-platform frameworks.

## 1. React Native with Expo
*   **The Managed Workflow:** Fast development, but how do you handle custom native code?
*   **Expo Dev Clients:** Moving beyond Expo Go to create custom development builds with native modules.
*   **EAS (Expo Application Services):** Using `eas build` for cloud-based iOS/Android builds and `eas submit` for store deployments.
*   **State Management in RN:** If the client uses BLoC in Flutter, they might prefer **Zustand** or **Redux Toolkit** in RN for its structured approach.

## 2. Flutter Web: CanvasKit vs. HTML
*   **HTML Renderer:** Uses standard HTML elements, CSS, and Canvas. Smaller download size, better for text-heavy sites.
*   **CanvasKit (Skia):** Compiles Skia to WebAssembly. Larger download (~2MB), but provides pixel-perfect consistency with mobile and high-performance animations.
*   **Strategy:** Use `--web-renderer auto` to let the browser decide, or force one based on the app type (e.g., dashboard vs. content site).

## 3. Responsive Design Challenges
*   **Context.select / Context.watch:** Efficiently rebuilding only relevant parts of the UI when the window size changes.
*   **Grid Systems:** Implementing a column-based grid system that works across 6.7" phones and 27" monitors.

## 4. Mobile-First vs. Responsive
While the focus is "Mobile-First," the requirement for Flutter Web means you must design for **Adaptability**.
- **Touch vs. Click:** Handling hover states on Web that don't exist on Mobile.
- **Form Factors:** Adapting complex mobile sheets into desktop modals or sidebars.

## Interview Questions
1.  "Why would a client choose Flutter over React Native today, and vice-versa?"
2.  "How do you handle 'responsive' images in Flutter Web to ensure fast loading?"
3.  "Describe your experience with Expo EAS. How has it improved the mobile CI/CD pipeline?"
4.  "What are the biggest performance pitfalls when building for the Web with Flutter?"
