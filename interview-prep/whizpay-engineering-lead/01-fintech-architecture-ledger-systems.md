# Deep Dive: FinTech Architecture & Ledger Systems

## 📖 Double-Entry Bookkeeping
In a financial-grade system, every transaction must have a corresponding and opposite entry in at least two accounts.
*   **Asset = Liability + Equity**
*   **Debits and Credits:** Every entry must balance.
*   **Immutable Entries:** You never "DELETE" or "UPDATE" a transaction. If a mistake is made, you issue a **Reversal Transaction**.

## 🏗️ PostgreSQL for Ledgers
PostgreSQL is the gold standard for financial data due to its ACID compliance.
*   **Transactions:** Use `SERIALIZABLE` or `REPEATABLE READ` isolation levels for critical ledger updates to prevent "Phantom Reads".
*   **Constraints:** Use `CHECK` constraints to ensure balances don't go negative (if applicable).
*   **Schema Design:**
    *   `Accounts` (id, type, balance)
    *   `Entries` (id, account_id, amount, direction [debit/credit], transaction_id)
    *   `Transactions` (id, timestamp, metadata)

## ⚡ Idempotency
In payments, a network timeout might occur after a request is processed but before the response is received.
*   **Idempotency Key:** The client sends a unique UUID (e.g., `X-Idempotency-Key`).
*   **Storage:** Store the result of the transaction mapped to this key. If the client retries with the same key, return the cached result instead of re-processing.

## 💡 Interview Q&A
**Q: How do you prevent 'Double Spending' in a wallet system?**
**A:** Use **Atomic Database Transactions**. Wrap the balance check and the balance update in a single `BEGIN...COMMIT` block. Use **Pessimistic Locking** (`SELECT FOR UPDATE`) on the account row to prevent other transactions from modifying the balance until the current one is finished.

**Q: Why is double-entry bookkeeping better than just having a 'balance' column?**
**A:** It provides an **Audit Trail**. You can reconstruct the balance at any point in history by summing the entries. It also makes "Reconciliation" (matching your internal records with external bank statements) possible.

## 🛠️ Code Snippet: Atomic Balance Update (TypeScript/TypeORM)
```typescript
await dataSource.transaction(async (transactionalEntityManager) => {
    // 1. Lock the sender's account row
    const sender = await transactionalEntityManager.findOne(Account, {
        where: { id: senderId },
        lock: { mode: 'pessimistic_write' }
    });

    if (sender.balance < amount) throw new Error('Insufficient funds');

    // 2. Perform updates
    sender.balance -= amount;
    await transactionalEntityManager.save(sender);

    // 3. Create ledger entries...
});
```
