# 05: Data Privacy & Compliance

Edge operates in high-compliance industries (Medical, Insurance), making privacy a top priority.

## 1. GDPR & Data Minimization
- **Principle:** Only collect what you need.
- **Data Scrubbing:** Removing PII (Personally Identifiable Information) before it hits the AI models or the Data Warehouse.

## 2. Consent Mode v2 (Google)
- **What is it?** A way to tell Google tags (GA4, Ads) how to behave based on user consent.
- **Advanced Mode:** Allows for "Cookieless Pings" (modeling data) even if consent is denied.
- **Implementation:** Integrated into GTM and Cookie Banners (like OneTrust or CookieBot).

## 3. Secure AI Architectures
- **Zero-Retention Policies:** Ensuring that data sent to LLM APIs (OpenAI/Anthropic) is not used for training.
- **PII Redaction:** Using libraries like `Presidio` to mask names/emails before sending text to an LLM.

## 4. Privacy-Compliant Tracking
- **First-party Cookies:** Better than third-party for privacy and reliability.
- **Server-side GTM:** Hides the user's IP address and other metadata from third-party trackers.

## 5. Potential Interview Questions
1. **How does Consent Mode v2 affect GA4 reporting?**
   - *Answer:* If a user denies consent, GA4 uses machine learning (Behavioral Modeling) to fill in the gaps, rather than losing the data entirely. However, you won't have individual-level tracking for those users.
2. **What steps would you take to make an AI pipeline HIPAA compliant?**
   - *Answer:* Ensure all data is encrypted (at rest and in transit). Sign a BAA (Business Associate Agreement) with cloud and AI providers. Implement strict access controls (IAM) and audit logs. Redact PII wherever possible.
3. **How do you handle "Right to be Forgotten" (GDPR) in a Data Warehouse?**
   - *Answer:* Implement a "Deletion Request" workflow that identifies the user's ID across all tables (Customers, Events, Leads) and deletes or anonymizes their records.
