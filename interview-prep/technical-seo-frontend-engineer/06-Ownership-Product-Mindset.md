# Ownership & Product Mindset

**Focus:** Identifying opportunities, independent execution, behavioral questions

---

### Q1: Identifying SEO Opportunities
**Question:** The JD says you should "Identify SEO opportunities and turn them into shipped pages, not just recommendations." Give me an example of how you would do this at a financial SaaS company like Wisesheets.

**Expected Answer:**
- **The Old Way (Recommendations)**: Run Ahrefs, find that "how to calculate WACC" has high volume. Write a Jira ticket for the content team to write a blog post. Wait 3 weeks.
- **The New Way (Shipping)**: 
  1. I notice the keyword volume for specific financial formulas is high.
  2. I realize we have the financial data API already built.
  3. I independently build a "WACC Calculator" template in Next.js.
  4. I use Claude to generate the explanatory text and schema.
  5. I launch the page within 48 hours.
  6. I monitor Google Search Console for impressions. If it works, I scale the template to 50 other financial metrics (DCF, ROIC, etc.).

---

### Q2: Handling Competing Priorities
**Question:** You find a massive technical SEO issue (e.g., thousands of soft 404s wasting crawl budget), but the founders want you to launch a new landing page for a marketing campaign tomorrow. How do you handle this?

**Expected Answer:**
- **Impact vs. Effort**: I assess the immediate business impact. A marketing campaign launch is time-sensitive and tied directly to revenue today. The soft 404s have been there for months; another 48 hours won't kill us.
- **Communication**: I tell the founders: "I'll ship the landing page for tomorrow's campaign. However, I found a critical crawl budget issue. I'm scheduling time on Thursday to fix it, as it's dragging down our overall organic visibility."
- **Ownership**: I don't ask for permission to fix the SEO issue later; I inform them of my prioritized plan.

---

### Q3: Working Independently
**Question:** This role requires a lot of autonomy. Describe a time you recognized a problem in a product, designed a solution, and shipped it without being told to do so.

**Expected Answer:**
- **STAR Method Example**:
  - **Situation**: At my last job, I noticed our internal linking was terrible—blog posts rarely linked to product features.
  - **Task**: We needed a way to automatically link high-intent keywords to our money pages.
  - **Action**: Without waiting for a ticket, I built a small Next.js middleware/plugin that parsed our MDX blog files. If it found the word "Options Trading", it automatically wrapped it in a link to our `/features/options` page.
  - **Result**: I shipped it, and within a month, our money pages saw a 15% lift in organic traffic due to the improved internal PageRank flow.

---

### Q4: Explaining Impact
**Question:** "Ability to explain what you built, why you built it, and what impact it had." Explain a recent technical SEO win using this framework.

**Expected Answer:**
- **What I built**: I migrated a client-side rendered (CSR) React dashboard into a Next.js Server-Side Rendered (SSR) architecture.
- **Why I built it**: Googlebot was struggling to execute the heavy JavaScript, meaning our core financial data wasn't being indexed, resulting in zero organic traffic for those pages.
- **The Impact**: TTFB improved by 40%, LCP dropped from 4s to 1.2s. More importantly, Google indexed 10,000 previously invisible pages, resulting in a 300% increase in organic non-branded search traffic within 60 days.

---

### Q5: Fixing Broken Systems
**Question:** "Someone who can look at a site, find what is broken, prioritize it, and fix it." Walk me through your mental checklist when you audit a site for the first time.

**Expected Answer:**
1. **Indexability**: Are `robots.txt` or `noindex` tags blocking critical pages? (Check Google Search Console Coverage report).
2. **Crawlability**: Is the XML sitemap accurate? Are there broken internal links or redirect chains? (Run Screaming Frog).
3. **Architecture**: Is it a Single Page App (SPA) hiding content from crawlers? Do they have canonical tags set up?
4. **Performance**: Run Lighthouse. Fix LCP and CLS issues first.
5. **Content**: Are there thousands of thin, low-value pages cannibalizing each other?
- *Prioritization*: Fix Indexability first (if Google can't see it, nothing else matters). Then fix Architecture/Canonicalization. Then Performance.
