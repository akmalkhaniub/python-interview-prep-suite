# Deep Dive: Backend Scaling & Modular Node.js

## 🏗️ Modular Monolith vs. Microservices
For a startup like WhizPay, a **Modular Monolith** is often superior to Microservices.
*   **Why?** Less operational overhead, simpler deployment, and strict type-safety across modules.
*   **Structure:** Separate domains (e.g., `Auth`, `Payments`, `Wallets`, `Ledger`) into distinct folders with their own services and controllers.

## 🚀 Node.js Performance for Finance
*   **Event Loop:** Avoid blocking the event loop with heavy crypto or math. Use `Worker Threads` for CPU-intensive tasks like generating large PDF reports or complex hashing.
*   **Precision Math:** Never use floating-point numbers (`0.1 + 0.2 !== 0.3`) for currency. Use **BigInt** (for subunits like cents/satoshi) or libraries like **Decimal.js**.

## 🛠️ NestJS Patterns
As a Lead, you should enforce strong architectural boundaries:
*   **Dependency Injection (DI):** Decouple business logic from external providers (e.g., `PaymentProvider` interface that can be implemented by `Stripe` or `CryptoCustodian`).
*   **Guards & Interceptors:** For cross-cutting concerns like Auth, Logging, and Idempotency.

## 💡 Interview Q&A
**Q: How do you handle TypeORM performance issues in a system with millions of transactions?**
**A:** 
1. Use **Query Builder** instead of the Repository pattern for complex joins.
2. Implement **Indexes** on frequently queried columns (`account_id`, `created_at`).
3. Use **Pagination** (Keyset/Cursor-based is better than Offset-based for large datasets).
4. Monitor slow queries using `EXPLAIN ANALYZE`.

**Q: Node.js is single-threaded. How does this impact a high-concurrency payment gateway?**
**A:** Node.js excels at I/O-bound tasks (API calls, DB queries). The single thread manages thousands of concurrent connections using an non-blocking event loop. As long as we don't perform heavy CPU work on the main thread, it can handle thousands of TPS (Transactions Per Second).

## 🛠️ Code Snippet: Money Handling with BigInt
```typescript
// Storing $10.50 as cents
const balance: bigint = 1050n;
const withdrawal: bigint = 500n;

function subtract(a: bigint, b: bigint): bigint {
    if (a < b) throw new Error('Insufficient funds');
    return a - b;
}

const newBalance = subtract(balance, withdrawal);
console.log(newBalance.toString()); // "550"
```
