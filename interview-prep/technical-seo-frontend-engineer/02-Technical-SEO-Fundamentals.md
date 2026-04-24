# Technical SEO Fundamentals

**Focus:** Indexing, canonicals, schema markup, sitemaps, robots.txt, internal linking

---

### Q1: Canonicals and Duplicate Content
**Question:** If we have a financial calculator available at `/calculator/compound-interest` and `/tools/compound-interest`, how do you handle this to prevent duplicate content penalties?

**Expected Answer:**
- **Canonical Tag**: Use the `<link rel="canonical" href="..." />` tag in the `<head>`.
- Both pages should exist if needed for user flow, but *both* should have a canonical tag pointing to the primary version (e.g., `/calculator/compound-interest`).
- This tells Google to consolidate ranking signals (like backlinks) to the primary URL and prevents the pages from cannibalizing each other in search results.
- **In Next.js**: Handled via the `metadata.alternates.canonical` property in the App Router.

---

### Q2: Managing Indexation (Robots.txt vs Noindex)
**Question:** We want to stop Google from indexing our user dashboard pages (`/dashboard/*`). Do you use `robots.txt` or a `noindex` tag? What is the difference?

**Expected Answer:**
- **`robots.txt` (`Disallow: /dashboard/`)**: Prevents Googlebot from *crawling* the URL. However, if the page is linked from elsewhere, Google might still index the URL itself (without knowing its content).
- **`noindex` tag (`<meta name="robots" content="noindex">`)**: Allows Googlebot to crawl the page, see the `noindex` directive, and ensures it is *removed* from or never added to the search index.
- **Correct approach**: Use the `noindex` meta tag for the dashboard. If you block it in `robots.txt`, Googlebot can't see the `noindex` tag. 
- *Rule of thumb*: Use `robots.txt` to save crawl budget on infinite filtering parameters; use `noindex` to keep private/thin pages out of search results.

---

### Q3: Schema Markup (Structured Data)
**Question:** How would you implement Schema markup for an investing calculator page, and why does it matter?

**Expected Answer:**
- **Why it matters**: Schema markup (JSON-LD) translates the page content into a machine-readable format for search engines, leading to Rich Snippets in SERPs (improving Click-Through Rate - CTR).
- **Implementation**: I would inject a JSON-LD script tag in the `<head>`.
- **Types to use**:
  - `SoftwareApplication` or `WebApplication` for the calculator itself.
  - `FAQPage` if the page includes a Frequently Asked Questions section about the math.
  - `BreadcrumbList` to help Google understand site architecture.
- **In Next.js**: Use the `next-seo` package or manually render a `<script type="application/ld+json">` tag containing stringified JSON, ensuring it's valid via Google's Rich Results Test tool.

---

### Q4: XML Sitemaps at Scale
**Question:** Wisesheets generates 50,000 stock data pages. How do you handle the XML sitemap?

**Expected Answer:**
- **Sitemap Limits**: A single XML sitemap can only hold 50,000 URLs and be up to 50MB.
- **Sitemap Index**: I would create a Sitemap Index file (`sitemap-index.xml`) that points to multiple child sitemaps (e.g., `sitemaps/stocks-1.xml`, `sitemaps/stocks-2.xml`).
- **Dynamic Generation**: Do not build this manually. In Next.js, use dynamic route handlers (e.g., `app/sitemap.ts`) connected to the database to generate these XML files on the fly or during the build process.
- **Prioritization**: Include `<lastmod>` tags strictly when the data actually changes to help Google allocate its crawl budget effectively.

---

### Q5: Internal Linking Architecture
**Question:** The JD asks you to improve internal linking. Why is this critical, and how do you implement it systematically?

**Expected Answer:**
- **Why it matters**: Internal links distribute "link equity" (PageRank) throughout the site, establish topic clusters, and ensure Googlebot can actually find orphaned pages.
- **Systematic Implementation**:
  - **Breadcrumbs**: Implement on every template to establish hierarchy.
  - **Related Content Component**: On a stock page for "Apple", automatically link to "Other Tech Stocks" or "Competitors (Microsoft, Google)". This must be driven by data relationships in the backend, not hardcoded.
  - **Contextual in-text links**: Build a CMS extension or markdown parser that automatically links specific keywords (e.g., "P/E Ratio") to their corresponding glossary pages.
  - **Footer/Header Navigation**: Ensure core money-pages are maximum 2 clicks away from the homepage.
