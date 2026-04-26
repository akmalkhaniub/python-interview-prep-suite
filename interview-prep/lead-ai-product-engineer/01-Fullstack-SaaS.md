# 01: Fullstack SaaS Architecture

This notebook focuses on the technical build-out of commercial B2B and B2C platforms using Next.js, Node.js, and Stripe.

## 1. Multi-tenant Architecture
In a SaaS environment, you must ensure data isolation.
- **Shared Database, Shared Schema (Firestore):** Use a `tenantId` field on every document. Enforce isolation via Security Rules.
- **Tenant Management:** Handling onboarding, custom domains (optional), and tenant-specific configurations.

## 2. Next.js Scaling Strategies
- **Server-Side Rendering (SSR):** For dynamic, user-specific data.
- **Incremental Static Regeneration (ISR):** For content-heavy pages (marketing, blogs) that need to be fast but updated occasionally.
- **Middleware:** For authentication checks, tenant routing, and Geo-IP features.

## 3. Stripe Integration (The Revenue Engine)
- **Checkout Sessions:** Fast way to handle subscriptions.
- **Webhooks:** CRITICAL for updating user status in Firebase when a payment succeeds or fails.
- **Usage-based Billing:** Reporting usage (e.g., tokens consumed) to Stripe for billing at the end of the cycle.
- **Customer Portal:** Allowing users to manage their own subscriptions without custom code.

## 4. Scalable Backend Patterns (Node.js)
- **API Routes (Next.js) vs. Separate Backend:** For a "Lead" role, you need to decide when to move logic out of Next.js into a dedicated Node.js or Python microservice.
- **Caching:** Using Redis or local cache for frequently accessed tenant settings.

## 5. Potential Interview Questions
1. **How do you prevent "noisy neighbor" problems in a shared multi-tenant environment?**
   - *Answer:* Implement rate limiting at the API gateway level. In Firestore, use security rules to limit query sizes and complexity. Use separate cloud functions/workers for heavy processing.
2. **Explain the lifecycle of a Stripe Webhook and how you ensure it's processed exactly once.**
   - *Answer:* Stripe sends a POST request. You verify the signature using the webhook secret. You check if the `eventID` has already been processed in your database (Idempotency). You return a 200 OK immediately and process the logic asynchronously.
3. **When would you choose Next.js App Router over Pages Router?**
   - *Answer:* For a new greenfield project, App Router is the standard. It provides better performance through React Server Components (RSC) and simplified data fetching.
