# 01 - UI/UX & Component Architecture

## Architecture

### 1. What is "Atomic Design"?
**Answer:**
A methodology for building design systems:
- **Atoms:** Basic building blocks (Input, Button, Label).
- **Molecules:** Groups of atoms (Search bar = Input + Label + Button).
- **Organisms:** Complex components formed by molecules (Header, Navbar).
- **Templates:** Page-level layouts.
- **Pages:** Specific instances of templates with real data.
- **Benefit:** Encourages consistency and maximum reusability.

### 2. Difference between "Presentational" and "Container" Components?
**Answer:**
- **Presentational (Dumb):** Concerned with *how things look*. They receive data via props and have no state/logic.
- **Container (Smart):** Concerned with *how things work*. They handle data fetching, state management, and pass data down to presentational components.
- **Note:** In modern React (Hooks), this separation is often achieved by moving logic into **Custom Hooks**.

### 3. How do you maintain a scalable CSS architecture?
**Answer:**
- **CSS Modules:** Scopes CSS to the component.
- **CSS-in-JS (Styled-components, Emotion):** Allows dynamic styling based on props.
- **Utility-First (Tailwind):** Provides rapid development with a predefined design system.
- **Variables/Design Tokens:** Essential for maintaining consistent colors, spacing, and typography.

## UI/UX Integration

### 4. How do you ensure "Pixel Perfection" from a Figma design?
**Answer:**
- Use **Inspect** mode in Figma to get exact values.
- Implement a **Base Grid** (e.g., 8pt grid) for spacing.
- Use **Browser Developer Tools** to compare the implementation against the design overlay.
- Ensure **Typography** (line-height, letter-spacing) matches exactly.

### 5. What are "Skeleton Screens" and why use them?
**Answer:**
A placeholder version of the UI that mimics the layout while data is loading.
- **Benefit:** Better perceived performance than a spinning loader; it makes the application feel faster and more stable.

### 6. How do you handle complex, data-intensive tables?
**Answer:**
- **Virtualization:** Rendering only the rows visible on the screen (`react-window`).
- **Pagination/Infinite Scroll.**
- **Sticky Headers/Columns.**
- **Filtering/Sorting** at the component level.
