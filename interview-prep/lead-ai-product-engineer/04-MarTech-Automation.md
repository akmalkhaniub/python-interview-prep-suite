# 04: MarTech Automation & Scraping

Building the "engine" that drives inbound growth.

## 1. Web Scraping for Context
To generate good content, the AI needs context.
- **Playwright/Selenium:** For scraping JS-heavy sites.
- **BeautifulSoup/LXML:** For fast, static HTML parsing.
- **Proxy Management:** Handling rate limits and IP blocks during mass scraping.

## 2. Automated Content Pipelines
1. **Source:** Scrape news, blogs, or social media.
2. **Process:** Clean data → Summarize via Claude → Enhance for SEO.
3. **Format:** Strict JSON for metadata, tags, and body.
4. **Deploy:** Post to WordPress, Ghost, or a custom Next.js CMS via API.

## 3. SEO-Native Software
- **Sitemap Generation:** Automating the discovery of new AI-generated pages.
- **Schema Markup:** Injecting `FAQPage`, `Article`, or `Product` JSON-LD automatically.
- **Internal Linking:** Using AI to find relevant pages to link to within the content.

## 4. Email & Outreach Automation
- Integrating with **Resend** or **SendGrid**.
- Using AI to personalize outreach based on scraped lead data.

## 5. Potential Interview Questions
1. **How do you handle "anti-scraping" measures like Cloudflare?**
   - *Answer:* Use "stealth" plugins for Playwright, rotating proxies, or specialized services like BrightData. Also, prioritize APIs where available.
2. **How do you ensure AI-generated content doesn't hurt SEO (Google's "Helpful Content" update)?**
   - *Answer:* Focus on "Value Addition". Use the AI to synthesize multiple sources, add unique data points, and ensure the formatting (H1, H2, etc.) is perfect. Human-in-the-loop for final review where necessary.
3. **Describe how you'd build a "Lead Magnet" generator.**
   - *Answer:* A user enters a URL → We scrape it → Claude identifies pain points → Claude generates a PDF/guide solving those points → Next.js delivers it in exchange for an email.
