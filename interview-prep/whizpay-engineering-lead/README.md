# Study Plan: Engineering Lead (WhizPay)

This module is focused on **FinTech Infrastructure**, specifically building cross-border payment rails using **Node.js/TypeScript** and **Blockchain (Stablecoins)**.

## 🎯 Role Overview
You are responsible for the "Financial Source of Truth". This means building systems where data integrity is non-negotiable.
1.  **Ledger Integrity:** Implementing immutable double-entry bookkeeping.
2.  **Payment Orchestration:** Bridging traditional banking (Fiat) and programmable finance (Crypto/USDC).
3.  **Startup Leadership:** Building 0→1 systems while mentoring a growing engineering team.

## 🛠️ Tech Stack Focus
*   **Backend:** Node.js, TypeScript, NestJS (preferred for modular architecture).
*   **Database:** PostgreSQL (Transactional integrity), TypeORM.
*   **Finance Concepts:** Double-entry bookkeeping, Reconciliation, KYC/AML flows.
*   **Crypto/Web3:** USDC (ERC-20/Solana), Custodial Wallets (Fireblocks/Cobo), On-ramps.

## 🗺️ Learning Roadmap

### 1. FinTech Architecture & Ledger Systems
*   [01-fintech-architecture-ledger-systems.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\whizpay-engineering-lead\01-fintech-architecture-ledger-systems.md)
*   **Topics:** Double-entry bookkeeping, ACID transactions in Postgres, Idempotency in payments.

### 2. Backend Scaling & Modular Node.js
*   [02-backend-scaling-nodejs-typescript.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\whizpay-engineering-lead\02-backend-scaling-nodejs-typescript.md)
*   **Topics:** Dependency Injection, TypeORM performance, Event-driven architecture, API Security.

### 3. Payment Rails & Blockchain Integration
*   [03-payment-rails-blockchain-integration.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\whizpay-engineering-lead\03-payment-rails-blockchain-integration.md)
*   **Topics:** Stablecoin lifecycles (Mint/Burn/Transfer), Custodial wallet APIs, Cross-border remittance patterns.

### 4. Security, Compliance & Audit Trails
*   [04-security-compliance-audit-trails.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\whizpay-engineering-lead\04-security-compliance-audit-trails.md)
*   **Topics:** JWT/OAuth2, KMS (Key Management), Immutable audit logs, PCI-DSS/SOC2 mindset.

## 🚀 Key Interview Questions
1.  "How do you design a double-entry ledger in PostgreSQL that ensures the 'Total Balance' always matches the 'Sum of Transactions'?"
2.  "Describe your strategy for handling 'Race Conditions' when two users try to withdraw from the same wallet simultaneously."
3.  "How do you implement idempotency for an API that talks to a 3rd party crypto custodian?"
4.  "What are the trade-offs between using a SQL database vs. a specialized Ledger database (like TigerBeetle) for a startup?"
