# Programmatic SEO & Architecture

**Focus:** Scaling content, pSEO strategies, avoiding thin/duplicate content, template systems

---

### Q1: Programmatic SEO (pSEO) Fundamentals
**Question:** Wisesheets wants to create 10,000 pages for different stock tickers (e.g., "AAPL Stock Financials", "TSLA Stock Financials"). How do you build this programmatic SEO system?

**Expected Answer:**
- **Data Source**: Connect to a robust database or external API (like financial data providers).
- **URL Structure**: Establish a clean pattern: `/stocks/[ticker]/financials`.
- **Dynamic Routing in Next.js**: Use dynamic segments (`app/stocks/[ticker]/page.tsx`).
- **Generation Strategy**: 
  - Do not use SSG for all 10,000 at build time (builds will take hours).
  - Use **ISR (Incremental Static Regeneration)** or **On-Demand Revalidation**. Pre-render the top 500 most popular stocks at build time, and generate the rest on the fly when first requested, caching them for subsequent users.
- **Template Design**: Create a modular template. The data injected into it must be robust enough that the page provides unique value, not just a table of numbers.

---

### Q2: Avoiding "Thin Content" Penalties
**Question:** When generating thousands of pages programmatically, how do you ensure Google doesn't penalize the site for "Thin Content" or "Doorway Pages"?

**Expected Answer:**
- **The Problem**: If a programmatic page only has a title ("AAPL Financials") and 3 data points, Google considers it low value and won't index it.
- **The Solution (Value Add)**:
  - **Data Aggregation**: Combine data from multiple sources (e.g., financial metrics + latest news API + sentiment analysis).
  - **Calculated Insights**: Don't just show "P/E Ratio: 25". Add programmatic text: "AAPL's P/E ratio of 25 is *higher* than the tech sector average of 20."
  - **Interactive Elements**: Add dynamic charts (CSR) that users can interact with.
  - **User-Generated Content**: If applicable, integrate user reviews or community discussions to make the page unique.

---

### Q3: Avoiding Duplicate Content in pSEO
**Question:** If the layout for "AAPL" and "TSLA" is identical, will Google consider it duplicate content?

**Expected Answer:**
- Layout duplication is expected and fine (every Amazon product page looks the same). 
- **Content Duplication** is the issue. If the programmatic text generation is exactly the same ("This is a company in the [Sector] sector..."), that is a problem.
- **Fix**: Use complex templating logic (Spintax or LLM-generated summaries) to ensure the narrative text surrounding the data is highly varied. Ensure the core data (the actual meat of the page) is distinct.

---

### Q4: Handling Empty States and 404s
**Question:** Your pSEO system tries to generate a page for a delisted stock ticker. How do you handle this for SEO?

**Expected Answer:**
- **Soft 404 Prevention**: Never return a page with a 200 OK status that says "Data not found". Google considers this a Soft 404, which wastes crawl budget.
- **Correct HTTP Status**: 
  - If the stock was *never* valid: Return a hard `404 Not Found`.
  - If the stock was valid but *delisted/removed*: Return a `410 Gone`. This tells Google to drop it from the index faster than a 404.
- **In Next.js**: Call the `notFound()` function in the Server Component if the database query returns null, which triggers the 404 UI and correct status code.

---

### Q5: Template-Driven Content Systems
**Question:** You need to manage 5 different pSEO templates (Stocks, ETFs, Calculators, Glossaries, Blog). How do you organize the codebase?

**Expected Answer:**
- **Component Driven**: 
  - `components/templates`: The wrapper layouts (e.g., `StockTemplate.tsx`).
  - `components/modules`: Reusable blocks (e.g., `FinancialTable`, `PriceChart`, `FAQBlock`).
- **Data Fetching Abstraction**: Keep API calls out of the UI components. Use a service layer (`lib/api/stocks.ts`) so templates just receive typed props.
- **Headless CMS Integration**: Use a CMS (Sanity, Contentful, or even localized JSON/MDX) to manage the metadata and static text portions of the templates, so non-developers (marketing) can tweak copy without deploying code.
