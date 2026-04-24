# AI-Assisted Development

**Focus:** Claude Code, AI workflows, velocity, shipping fast

---

### Q1: Using Claude Code for Velocity
**Question:** The JD states "strong experience using Claude code is a must." How do you use AI tools like Claude to ship faster without sacrificing code quality?

**Expected Answer:**
- **Boilerplate & Bootstrapping**: I use Claude to instantly generate repetitive code (e.g., TypeScript interfaces, basic component scaffolding, or JSON-LD schema structures).
- **Complex Logic**: For financial math (e.g., calculating CAGR or Black-Scholes), I ask Claude to write the core pure function and the corresponding Jest unit tests, which saves hours of manual math verification.
- **Refactoring**: I use it to refactor messy components. I paste a 500-line component and ask it to "extract the data fetching logic into a custom hook and abstract the UI into smaller sub-components."
- **Verification**: I *never* blindly copy-paste. AI hallucinates APIs. I treat Claude as a highly capable junior developer—I review its code, ensure it aligns with our architecture, and verify it with tests.

---

### Q2: AI for SEO Automation
**Question:** How can we use AI to help with our programmatic SEO strategy?

**Expected Answer:**
- **Content Spintax/Summarization**: As discussed in pSEO, we need unique text surrounding data tables to avoid thin content penalties. We can build an ingestion pipeline where Claude/OpenAI summarizes raw financial data into a cohesive 2-paragraph overview for each of the 5,000 stocks.
- **Metadata Generation**: Automatically generating optimized `<title>` and `<meta name="description">` tags based on page content.
- **Schema Generation**: We can prompt Claude with a block of text (like an FAQ section) and ask it to output valid `FAQPage` JSON-LD schema, eliminating manual formatting.

---

### Q3: Debugging with AI
**Question:** You encounter a strange hydration error in Next.js that only happens in production. How does Claude fit into your debugging workflow?

**Expected Answer:**
- **Context Injection**: I don't just ask "how to fix hydration error." I paste the exact error stack trace, the relevant React component code, and the Next.js version into Claude.
- **Rubber Ducking**: I use it as a sounding board. "I suspect the `window.innerWidth` check inside the `useEffect` is running too late. What do you think?"
- **Regex/Bash**: I often use it to quickly generate complex grep commands or regex to search the codebase for specific anti-patterns causing the bug.

---

### Q4: Shipping Fast vs Perfect Architecture
**Question:** We need to test a new "Retirement Calculator" page by Friday to catch an SEO trend. It's currently Tuesday. How do you approach this using AI tools?

**Expected Answer:**
- **Scope Reduction**: Perfect architecture is the enemy of shipping fast. We don't need a generalized calculator engine by Friday. We need *one* working calculator.
- **AI Acceleration**:
  1. I ask Claude to generate the HTML/Tailwind shell based on a mockup.
  2. I ask Claude to generate the compounding math logic.
  3. I manually wire the state to the UI to ensure no UX bugs.
- **SEO Baseline**: Ensure the URL, Title, H1, and Meta Description are perfect. The calculator math must be accurate, but the underlying component doesn't need to be perfectly abstracted. We can refactor and componentize it next week *if* the page gets traffic.
