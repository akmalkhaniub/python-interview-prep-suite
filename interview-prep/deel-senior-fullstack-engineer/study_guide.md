# Study Guide: Senior Full Stack Engineer (Deel)

This guide covers technical and architectural topics relevant to the Senior Full Stack role at Deel, focusing on global payroll, compliance, and high-scale SaaS.

---

## 🏗️ 1. Frontend: React & TypeScript

### Q1: Explain the benefits of using TypeScript with React in a large-scale application like Deel.
- **Type Safety**: Catches errors during development (e.g., passing a string instead of a number to a payroll calculation component).
- **Interface Documentation**: Clear definitions of API responses (e.g., `interface Employee { id: string; salary: number; currency: Currency; }`) serve as living documentation.
- **Refactoring Confidence**: Easier to change property names or move logic across a massive codebase.
- **Enhanced IDE Support**: Autocomplete and type hints for complex props and state.

### Q2: How do you optimize a React component that renders a large list of employees?
- **Windowing/Virtualization**: Use `react-window` or `react-virtualized` to only render visible rows.
- **Memoization**: Use `React.memo` to prevent unnecessary re-renders of list items.
- **Key Optimization**: Ensure stable and unique keys (avoiding array index).
- **Batching Updates**: Use the latest React version's automatic batching.

### Q3: What is your approach to handling complex forms (e.g., employee onboarding with 50+ fields)?
- **Controlled vs. Uncontrolled**: Use libraries like `react-hook-form` or `Formik` to manage validation and state efficiently.
- **Dynamic Field Injection**: Use schema-based form builders for different jurisdictions (compliance varies by country).
- **Persistence**: Save drafts in local storage or the backend to prevent data loss.

---

## ⚙️ 2. Backend: Node.js, Express & NestJS

### Q1: Describe the architecture of a typical Express/NestJS microservice at scale.
- **Layered Architecture**: Controller (API endpoints) -> Service (Business logic) -> Repository (Database interaction).
- **Middleware**: For authentication, logging, error handling, and rate limiting.
- **Validation**: Using `class-validator` or `Joi` to ensure data integrity before processing.
- **Asynchronous Tasks**: Offloading heavy work (generating PDF invoices, sending emails) to background workers using Redis/Bull or RabbitMQ.

### Q2: How do you handle multi-currency transactions reliably in Node.js?
- **Floating Point Issues**: NEVER use `float/double` for money. Use `Decimal.js` or store amounts in cents (as `BigInt` or `string`).
- **Precision**: Maintain high precision (e.g., 8 decimal places) during internal calculations and round only at the final step.
- **Idempotency**: Use idempotency keys for all financial transactions to prevent double-charging.

---

## 🗄️ 3. Database: PostgreSQL

### Q1: How do you design a schema for "Global Compliance" where rules change per country?
- **EAV (Entity-Attribute-Value)** or **JSONB**: For flexible, country-specific fields that change frequently.
- **Parent-Child Tables**: Base `employee` table with linked `employment_contract_details` that reference a `country_regulation_id`.
- **Versioning**: Maintain a history of regulatory changes using effective dates (temporal tables).

### Q2: How would you optimize a slow query fetching payroll data for 10,000 employees?
- **Indexing**: Check for missing indexes on foreign keys and frequently filtered columns (e.g., `company_id`, `payroll_status`).
- **Query Refactoring**: Avoid `SELECT *`. Only fetch required columns. Use `EXPLAIN ANALYZE` to identify bottlenecks.
- **Materialized Views**: For complex reports that don't need real-time data.
- **Pagination**: Implement cursor-based pagination for large datasets.

---

## 🧪 4. Testing & Quality

### Q1: What is your strategy for testing a critical payroll calculation engine?
- **Unit Tests**: 100% coverage for the core math logic using Jest.
- **Integration Tests**: Verify database interactions and API responses.
- **Property-Based Testing**: Use `fast-check` to test with a wide range of random inputs to find edge cases.
- **Snapshot Testing**: For UI components in Storybook to detect regressions.

### Q2: Explain the role of Cypress in Deel's E2E testing.
- **User Flows**: Testing the entire flow from "Onboarding" to "First Payment".
- **Real Browser Testing**: Catching browser-specific CSS or JS issues.
- **CI/CD Integration**: Blocking deployments if critical business paths are broken.

---

## ☁️ 5. System Design & SaaS

### Q1: How would you design a notification system for Deel that handles global time zones?
- **Centralized Service**: A dedicated service with a queue (RabbitMQ/SQS).
- **User Preferences**: Store user's preferred time zone and notification channels.
- **Scheduling**: Use a job scheduler (e.g., BullMQ) that calculates the execution time based on the user's local time.
- **Dead Letter Queues**: To handle and retry failed notifications.

### Q2: How do you ensure high availability (99.9%) for a global SaaS like Deel?
- **Multi-region Deployment**: Deploying across different AWS/Azure regions.
- **Load Balancing**: Using global balancers to route traffic to the nearest healthy instance.
- **Auto-scaling**: Scaling horizontally based on CPU/Memory usage.
- **Database Replicas**: Read replicas for performance and failover nodes for recovery.

---

## 🤝 6. Soft Skills & Culture

### Q1: Deel is remote-first. How do you handle communication in a distributed team?
- **Asynchronous First**: Using Slack, Jira, and Loom for non-urgent updates.
- **Documentation**: "If it's not written down, it doesn't exist." Detailed ADRs (Architecture Decision Records) and RFCs.
- **Overlap Hours**: Scheduling synchronous meetings during hours that work for most time zones.

### Q2: What does "Taking Ownership" mean to you as a Senior Engineer?
- **End-to-End Responsibility**: Not just writing code, but ensuring it's tested, deployed, monitored, and documented.
- **Mentorship**: Helping junior/mid-level engineers grow.
- **Proactive Troubleshooting**: Identifying potential issues before they become bugs.
