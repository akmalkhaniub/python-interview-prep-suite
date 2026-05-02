# 04 - Authentic Engineering & Debugging

## Senior Engineering Mindset

### 1. How do you approach a "Sophisticated Bug" in a 1M+ line codebase?
**Answer:**
- **Reproduce:** Create a minimal test case that consistently fails.
- **Isolate:** Use `git bisect` to find the commit that introduced the bug.
- **Trace:** Use debuggers (GDB, PDB) and distributed tracing (Jaeger).
- **Hypothesize:** Form multiple theories and test them one by one.
- **Fix & Prevent:** Fix the bug and add a regression test.

### 2. What is "Code Refactoring" and when is it necessary?
**Answer:**
Improving the internal structure of code without changing its external behavior.
- **Necessity:** When "Technical Debt" slows down development, when the code is hard to test, or when performance bottlenecks are identified.

### 3. Importance of "Architectural ADRs" (Architecture Decision Records)?
**Answer:**
A document that captures a significant architectural decision, including its context, consequences, and alternative options. It provides a historical record of *why* things were built a certain way.

## Best Practices

### 4. What is "Defensive Programming"?
**Answer:**
Writing code that anticipates and handles unexpected inputs or states (e.g., input validation, error handling, assertions).

### 5. How do you lead a "Code Review" for a complex feature?
**Answer:**
- Focus on architectural soundness and security.
- Check for test coverage.
- Provide constructive, objective feedback.
- Look for "hidden" complexities (e.g., race conditions, memory leaks).

### 6. Importance of "Testing in Production" (Canary, Blue-Green)?
**Answer:**
- **Blue-Green:** Deploying a new version alongside the old one and switching traffic.
- **Canary:** Deploying to a small percentage of users first.
- **Benefit:** Reduces the risk of a full-scale outage.
