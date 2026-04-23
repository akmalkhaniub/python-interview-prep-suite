# Code Review, Standards & Governance

**Focus:** Code review guidelines, technical/coding standards, product-driven standards

---

### Q1: Code Review Guidelines
**Question:** As Chief Architect, how do you establish and enforce code review standards across a distributed team?

**Expected Answer:**
- **Written guidelines**: A living document covering what reviewers should check — correctness, readability, performance, security, test coverage.
- **Checklist by PR type**: Feature PRs check different things than bugfix or refactoring PRs.
- **Review SLA**: PRs reviewed within 24 hours. No PR sits longer than 48 hours.
- **Required reviewers**: 2 approvals minimum. Architect approval required for changes to core/shared libraries.
- **Automated checks first**: Linters, analyzers, tests must pass BEFORE human review. Don't waste reviewer time on formatting.
- **Review culture**: Comments are suggestions, not attacks. Use "nit:", "question:", "blocker:" prefixes.
- **PR size**: Max 400 lines changed. Larger PRs get split. Small PRs get faster, better reviews.

---

### Q2: Defining Technical Standards
**Question:** How do you create coding standards that the team actually follows rather than ignores?

**Expected Answer:**
- **Automate everything possible**: If a rule can be enforced by a Roslyn analyzer or `.editorconfig`, don't rely on humans.
- **ADRs (Architecture Decision Records)**: Document WHY a standard exists, not just WHAT it is. Context matters.
- **Start with existing standards**: Microsoft's .NET design guidelines as baseline. Customize for your product.
- **Team buy-in**: Propose standards as RFCs. Let developers comment and vote. Imposed standards get ignored.
- **Living document**: Standards evolve. Review quarterly. Deprecate outdated rules.
- **Exceptions process**: Standards must allow exceptions with justification. Rigid rules create resentment.
- **Onboarding**: New developers go through standards walkthrough in first week.

---

### Q3: Architecture Decision Records (ADRs)
**Question:** Show me an example ADR for a technology choice.

**Expected Answer:**
```markdown
# ADR-007: Use MediatR for CQRS Implementation

## Status: Accepted

## Context
Our ERP modules need clear separation between read and write operations.
Multiple cross-cutting concerns (logging, validation, caching) need
to be applied consistently across all handlers.

## Decision
We will use MediatR with pipeline behaviors for all Application layer commands and queries.

## Consequences
- (+) Decoupled handlers, easy to test individually
- (+) Pipeline behaviors replace manual boilerplate
- (-) Indirect dispatch makes "Find References" harder
- (-) Team needs training on the pattern
- (-) Risk of over-abstracting simple CRUD

## Alternatives Considered
- Direct service calls: Simpler but tightly coupled
- Custom mediator: More control but maintenance burden
```

---

### Q4: Managing Technical Debt
**Question:** The ERP has accumulated years of tech debt. How do you quantify it and convince management to allocate time for it?

**Expected Answer:**
- **Quantify**: SonarQube "tech debt" estimate (hours to fix). NDepend for architectural violations.
- **Categorize**: Reckless vs. Prudent debt. "We knew this was a shortcut" vs. "We didn't know better."
- **Business impact**: "This module has 2x the bug rate because of XYZ. Fixing it saves 10 hours/sprint in bug triage."
- **20% rule**: Negotiate 20% of each sprint for tech debt and refactoring.
- **Debt backlog**: Track as first-class items alongside features. Tag in Jira/Azure DevOps.
- **Prevention**: Definition of Done includes "no new tech debt without ADR justification."
- **Metrics**: Track bug rate per module, build time, deployment frequency — show improvement after debt reduction.

---

### Q5: Evaluating New Technologies
**Question:** A developer proposes switching from SQL Server to PostgreSQL for cost savings. How do you evaluate this?

**Expected Answer:**
- **RFC process**: Developer writes a 1-2 page proposal: Problem, Options, Pros/Cons, Migration Effort, Risk.
- **Evaluation criteria**: Cost, team expertise, licensing, cloud provider support, tooling (EF Core support for both), migration effort.
- **Proof of Concept**: 2-week spike. Port one module's data layer. Benchmark performance.
- **Risk assessment**: What breaks? Stored procedures? Full-text search? Licensing agreements?
- **Migration plan**: Dual-write period, data migration scripts, rollback strategy.
- **Decision**: Present findings to leadership with clear recommendation and trade-offs.
- **ERP consideration**: 45,000 users — any DB change needs zero-downtime migration strategy.
