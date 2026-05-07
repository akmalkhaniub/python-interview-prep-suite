# Deep Dive: Payment Rails & Blockchain Integration

## 🌉 Bridging Fiat and Stablecoins
WhizPay moves money across "banking rails" and "blockchain systems".
*   **Traditional Rails:** SWIFT, SEPA, ACH, Local Payouts (e.g., Raast in Pakistan).
*   **Blockchain Rails:** USDC on Ethereum, Polygon, or Solana.

## 💼 Custodial Wallets
Instead of managing raw private keys, startups use providers like **Fireblocks**, **Cobo**, or **Circle**.
*   **Webhooks:** These providers send webhooks when a deposit is detected.
*   **Approval Policies:** Programmatic rules (e.g., "Any transaction > $10k requires human approval in the WhizPay dashboard").

## 🔄 Stablecoin Lifecycle
1.  **On-ramp:** User pays Fiat -> WhizPay triggers a Mint or Transfer of USDC to the user's wallet.
2.  **Settlement:** Moving USDC between business wallets.
3.  **Off-ramp:** User swaps USDC for local currency -> WhizPay triggers a local bank transfer.

## 💡 Interview Q&A
**Q: How do you handle 'Reorgs' (Chain Reorganizations) in your crypto payment system?**
**A:** Never consider a transaction "Final" after 1 block. Wait for a specific number of **Confirmations** (e.g., 3 on Solana, 12 on Ethereum) before updating the user's balance in your ledger.

**Q: A webhook from your payment aggregator failed to deliver. How do you recover?**
**A:** Implement a **Reconciliation Worker**. Periodically (e.g., every hour) query the aggregator's API for all transactions in the last 2 hours and compare them against your internal database. If a record is missing, trigger the "Finalize" logic manually.

## 🛠️ Code Snippet: Webhook Signature Validation
```typescript
import { createHmac } from 'crypto';

function isValidSignature(payload: string, signature: string, secret: string): boolean {
    const hmac = createHmac('sha256', secret);
    const digest = hmac.update(payload).digest('hex');
    return digest === signature;
}
// ALWAYS validate signatures to prevent "Fake Deposit" attacks
```
