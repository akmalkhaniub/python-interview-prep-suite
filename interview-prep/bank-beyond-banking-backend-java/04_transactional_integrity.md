# Transactional Integrity & Reconciliation

For a Bank, "Transactions" are the heart of the system. This guide covers ACID, distributed consistency, and reconciliation.

---

## 1. Distributed Transactions & ACID
*   **ACID in Microservices:** While local database transactions are ACID, distributed ones are **BASE** (Basically Available, Soft state, Eventual consistency).
*   **The Challenge:** Handling "Partial Fulfillment." 
    *   *Example:* User buys 3 items in the marketplace. 2 are in stock, 1 is not. 
    *   *Bank Logic:* We must charge for 2, trigger a partial refund for the 1, and ensure the ledger matches exactly.
*   **Talking Point:** "I design for **Atomic Consistency** at the service level and **Eventual Consistency** at the ecosystem level, using the Saga pattern to ensure that the user's balance always reflects the true state of their purchases."

## 2. Reversals & Refunds
*   **Reversal:** An immediate cancellation of a transaction (e.g., timeout during payment). Usually requires "Voiding" the transaction before settlement.
*   **Refund:** A financial movement after settlement.
*   **Logic:** A refund must be linked to the original `transaction_id`. We must verify that the refund amount does not exceed the original settled amount.
*   **Talking Point:** "Handling refunds in a Beyond Banking ecosystem requires tight linkage between the partner's 'Order ID' and the bank's 'Transaction ID.' I implement a reconciliation service that cross-references these daily."

## 3. Reconciliation (The "Source of Truth")
*   **Internal Reconciliation:** Matching the transaction logs in the `MarketplaceService` with the ledger entries in the `CoreBankingService`.
*   **External Reconciliation:** Matching the bank's "Settlement Report" with the partner's "Sales Report" (e.g., from Amadeus).
*   **The "Architectural" Approach:**
    1.  **Daily Batch:** Ingest partner reports into a staging area.
    2.  **Matching Engine:** Use high-performance SQL/BigQuery to identify mismatches.
    3.  **Exception Queue:** Flag records that don't match for manual investigation or automated "Correction Workflows."

## 4. Edge Case Handling
*   **Network Timeouts:** The most dangerous scenario. Did the partner receive the payment or not?
    *   *Solution:* **Querying the Partner.** Before failing the transaction, call a "Check Status" API on the partner's side to verify the outcome.
*   **Partial Success:** Payment succeeded, but the eSIM provisioning failed.
    *   *Solution:* Automatically trigger a reversal of the payment and notify the user with a specific "Provisioning Failed" message.

---

## 5. Security-by-Default
*   **Zero-Trust:** Never trust the data coming from a partner API. Validate every field using strict Pydantic/Zod-like schemas in Java (using Bean Validation / Jakarta EE).
*   **Encryption:** Use **AES-256** for sensitive data at rest (e.g., in the database) and **TLS 1.3** for data in transit.
*   **Token Scoping:** A "Travel Platform" token should only have permission to query travel-related APIs, never the user's full account balance.
