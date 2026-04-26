# 01: GA4 Mastery & BigQuery Analytics

Edge requires end-to-end ownership of GA4, from event design to BigQuery analysis.

## 1. GA4 Event Schema Design
Unlike Universal Analytics, GA4 is **Event-based**.
- **Recommended Events:** `sign_up`, `login`, `generate_lead`.
- **Custom Parameters:** Adding `plan_type`, `user_industry`, or `source_campaign` to events.
- **User Properties:** Persistent traits about the user (e.g., `is_premium: true`).

## 2. GTM Server-Side (sGTM)
- **Why?** It moves the tracking logic from the browser to a server (on GCP).
- **Benefits:** Better performance (less JS in browser), bypasses ad-blockers (sometimes), and allows for better data scrubbing before sending to GA4.

## 3. BigQuery Export
GA4 data in BigQuery is highly nested. You must master `UNNEST`.

### Example: Finding custom parameter values
```sql
SELECT
  event_name,
  (SELECT value.string_value FROM UNNEST(event_params) WHERE key = 'source_campaign') as campaign
FROM
  `your-project.analytics_12345.events_*`
WHERE
  event_name = 'lead_generated'
```

## 4. Marketing Funnels & Attribution
- **Funnel Analysis:** `page_view` -> `trial_start` -> `subscription_complete`.
- **Attribution:** Using `traffic_source` fields in BigQuery to understand which ads are driving revenue.

## 5. Potential Interview Questions
1. **Explain the difference between a "Session" in GA4 vs UA.**
   - *Answer:* In UA, a session ends at midnight or when a campaign change occurs. In GA4, sessions are calculated based on the `session_start` event, making them more resilient and accurate for cross-device tracking.
2. **How would you handle "Duplicate Events" in BigQuery?**
   - *Answer:* Use `event_bundle_sequence_id` or a combination of `user_pseudo_id`, `event_name`, and `event_timestamp` to deduplicate using `QUALIFY ROW_NUMBER() OVER(...) = 1`.
3. **What is the benefit of linking BigQuery to GA4 for a company like Edge?**
   - *Answer:* It allows us to join marketing data (GA4) with internal CRM data (Salesforce/HubSpot) to see the *true* ROI of a campaign beyond just "clicks".
