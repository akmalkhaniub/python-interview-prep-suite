# Technical Study Guide: Full Stack Marketing Engineer

## 1. Marketing Tech Deep Dive (CAPI & Pixel)

### Meta Conversion API (CAPI)
- **Concept:** Server-to-server tracking to bypass browser limitations (ad blockers, iOS 14+ privacy).
- **Key Fields:** `event_name`, `event_time`, `user_data` (hashed email, phone, IP, User Agent), `action_source`.
- **Deduplication:** The `event_id` must match between the Browser Pixel and the Server CAPI for the same event.
- **Python Implementation:** Familiarize with the `facebook-business` SDK or sending POST requests to `/events`.

### Attribution Models
- **Last Click vs. First Click:** Understanding how platforms credit revenue.
- **ROAS & CPL:** Calculating Return on Ad Spend and Cost Per Lead in real-time.

## 2. CRM Automation & Webhooks

### GoHighLevel (GHL) / HubSpot Integration
- **Webhooks:** Handling incoming data from CRM triggers (e.g., "Lead Status Changed").
- **Auth:** OAuth2 flows for CRM API access.
- **Python Backend:** Using FastAPI or Flask to create endpoints that process lead data and route it to other systems.

### Pipeline Management
- **Lead Routing Logic:** Implementing round-robin or weighted distribution of leads to sales teams.
- **Conversion Syncing:** Sending "Won" status from CRM back to Meta CAPI as an `OfflineConversion`.

## 3. AI in Marketing Workflows

### OpenAI API Integration
- **Ad Copy Generation:** Prompt engineering for varying hooks, bodies, and CTAs.
- **Lead Scoring:** Using LLMs to categorize lead quality based on form responses or chat transcripts.
- **Reporting Automation:** Using AI to summarize campaign performance data into "Actionable Insights" for media buyers.

### Python AI Stack
- **LangChain/LlamaIndex:** Orchestrating complex AI workflows.
- **Structured Outputs:** Using Pydantic with OpenAI to get JSON responses for lead categorization.

## 4. Full Stack Development (Next.js & Python)

### Next.js 14/15 Patterns
- **Server Components:** Optimizing performance for data-heavy dashboards.
- **API Routes:** Building the bridge between the frontend and Python microservices.
- **Visualizations:** Using Tremor, Shadcn/UI, or Chart.js for marketing dashboards.

### Python Backend
- **Data Pipelines:** Using `Pandas` or `Polars` to aggregate data from Google Ads, Meta Ads, and CRMs.
- **Background Tasks:** Using `Celery` or `Redis Queue` for long-running automation tasks (e.g., batch processing 10,000 leads).

## 5. Potential Interview Questions

### Technical
1. "How do you handle the technical implementation of Meta CAPI deduplication?"
2. "Describe how you'd build a Next.js dashboard that pulls data from the Meta Ads API and Google Ads API simultaneously."
3. "Explain your approach to securing webhooks from a CRM like GoHighLevel."
4. "How would you use an LLM to automatically flag 'garbage' leads in real-time?"

### Domain-Specific
1. "How do iOS privacy changes impact our ability to track conversions, and how does CAPI help?"
2. "What are the most important KPIs for a performance marketing agency, and how do you ensure their accuracy?"
3. "A media buyer says their dashboard data doesn't match the Meta Ads Manager. How do you troubleshoot this?"
