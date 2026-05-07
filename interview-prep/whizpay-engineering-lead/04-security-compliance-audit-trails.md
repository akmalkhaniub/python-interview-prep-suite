# Deep Dive: Security, Compliance & Audit Trails

## 🛡️ Security Fundamentals
Financial data requires a multi-layered security approach.
*   **Data at Rest:** All sensitive columns (PII, Bank Details) should be encrypted in the database.
*   **Data in Transit:** TLS 1.3 for all API communication.
*   **Secrets Management:** Use **AWS Secrets Manager** or **HashiCorp Vault**. Never use `.env` files in production.

## 📝 Immutable Audit Logs
An audit trail answer: "Who did what, and when?"
*   **Technique:** Use a dedicated `audit_logs` table that is **Append-Only**. 
*   **Structure:** `id`, `user_id`, `action_type`, `resource_id`, `before_state` (JSON), `after_state` (JSON), `ip_address`.

## 🏛️ Compliance Mindset (KYC/AML)
*   **KYC (Know Your Customer):** Integration with providers like Sumsub or Onfido.
*   **AML (Anti-Money Laundering):** Screening transactions against "Sanctions Lists". 
*   **Reporting:** Ability to generate SARs (Suspicious Activity Reports) for regulators.

## 💡 Interview Q&A
**Q: How would you store a user's API Key securely in WhizPay?**
**A:** Never store it in plain text. Store a **Hashed version** (using Argon2 or bcrypt) for verification. If the key needs to be used to call an external service *on behalf* of the user, encrypt it with a **Master Key** stored in a hardware security module (HSM) or KMS.

**Q: Describe the difference between Authentication and Authorization in a payments API.**
**A:** **Authentication** is "Who are you?" (JWT, API Key). **Authorization** is "What are you allowed to do?" (RBAC - Role Based Access Control). A user might be authenticated, but not authorized to "Refund" a transaction.

## 🛠️ Code Snippet: Append-Only Audit Log Pattern
```typescript
async function logAction(userId: string, action: string, data: any) {
    const log = new AuditLog();
    log.userId = userId;
    log.action = action;
    log.metadata = JSON.stringify(data);
    log.createdAt = new Date();
    
    // We don't await this if we don't want to block the main flow
    // or we send it to a message queue (SQS)
    await auditLogRepository.save(log);
}
```
