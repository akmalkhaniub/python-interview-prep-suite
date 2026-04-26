# 04: CRM Integrations & Webhooks

Connecting the data and AI systems to the tools the business actually uses.

## 1. HubSpot & Salesforce APIs
- **OAuth 2.0:** Securely connecting to these platforms.
- **Common Objects:** `Contacts`, `Companies`, `Deals`, `Custom Objects`.
- **Search API:** Finding existing records before creating new ones (avoiding duplicates).

## 2. Webhooks & Event-Driven Design
- **HubSpot Webhooks:** "When a deal moves to 'Closed Won', trigger a workflow."
- **Handling Webhooks:** Using FastAPI or Cloud Functions to receive the POST request, verify the source, and process the payload.
- **Retries:** What if the target system is down? Using a queue (Pub/Sub) to handle retries gracefully.

## 3. Syncing Data to BigQuery
- **Fivetran / Airbyte:** The easiest way to sync CRM data to BigQuery.
- **Custom Scripts:** If Fivetran is too expensive, writing Python scripts that call the `v3/objects/contacts` API and load JSON into BQ.

## 4. Alerting & Monitoring
- **Slack Integration:** Sending critical alerts (e.g., "Integration failed for Salesforce Sync").
- **Health Checks:** A periodic task that checks if the APIs are responding and the data is flowing.

## 5. Potential Interview Questions
1. **How do you handle API Rate Limits (e.g., Salesforce has strict limits)?**
   - *Answer:* Use exponential backoff for retries. Implement a "leaky bucket" or "token bucket" rate limiter in the code. Batch requests where possible (e.g., updating 100 contacts at once).
2. **Explain the difference between a Webhook and Polling.**
   - *Answer:* Polling is "Are we there yet?" (Inefficient). Webhooks are "Call me when you get there" (Efficient, real-time).
3. **How do you ensure data consistency between HubSpot and your Data Warehouse?**
   - *Answer:* Use a reliable ELT tool with "incremental sync" based on `updated_at` timestamps. Periodically run a "full sync" or "sanity check" to find discrepancies.
