# Deep Dive: Integrations & Multi-tenant Architecture

Success in this role depends on your ability to integrate third-party services securely and scale a multi-tenant SaaS.

## 1. Webhook Security (HMAC Validation)
*   **The Concept:** External services (like Stripe or Tap) send a signature in the header. You must hash the request body with a shared secret to verify the signature matches.
*   **Implementation (Node.js):**
    ```javascript
    const crypto = require('crypto');
    const signature = request.headers['x-tap-signature'];
    const hmac = crypto.createHmac('sha256', SECRET_KEY);
    const digest = hmac.update(JSON.stringify(request.body)).digest('hex');
    if (signature === digest) { /* Valid */ }
    ```
*   **Error Handling:** Always return a 200 OK to the webhook provider quickly, then process the logic asynchronously in a Cloud Function to avoid timeouts.

## 2. WhatsApp Business API (360dialog)
*   **The Setup:** Using a broker like 360dialog to access the Meta WhatsApp Business API.
*   **Templates:** Learning how to send "Template Messages" (e.g., "Your order is ready") which must be pre-approved by Meta.
*   **Webhooks:** Handling incoming messages from customers and routing them to the correct Firestore "Chat" document based on their phone number.

## 3. Multi-tenant Architecture in SaaS
*   **Data Isolation:** Ensuring Restaurant A cannot see Restaurant B's data via Security Rules.
*   **Scalability:** Structuring the DB so that adding 100 new franchises doesn't slow down global queries.
*   **Branding:** Handling tenant-specific configurations (logo, Arabic vs. English preference, tax rates) in a central `config` collection.

## 4. Arabic RTL Layout (The Competitive Advantage)
*   **Logical Properties:** Using `margin-inline-start` instead of `margin-left` so that the margin automatically flips when the direction changes.
*   **React Context:** Providing a `LanguageContext` that toggles the `dir` attribute on the root element.
*   **Icon Mirroring:** Knowing which icons to mirror (e.g., back arrows) and which to keep (e.g., clocks, checkmarks).

## 5. Deployment Discipline
*   **Environments:** Using Firebase projects for `development`, `staging`, and `production`.
*   **CI/CD:** Using GitHub Actions to run tests and then deploy to the correct environment based on the branch (`master` -> production).

## Interview Questions
1.  "How do you validate a webhook's authenticity if the provider doesn't use HMAC?" (e.g., IP whitelisting or shared tokens).
2.  "Describe the 'Franchise HQ' dashboard requirements in a multi-tenant system. How do you aggregate data across 50 restaurants?"
3.  "What are the challenges of supporting Arabic RTL in a React app, and how do you solve them?"
4.  "How do you handle a payment webhook that arrives *before* the user's frontend has finished the checkout process?"
