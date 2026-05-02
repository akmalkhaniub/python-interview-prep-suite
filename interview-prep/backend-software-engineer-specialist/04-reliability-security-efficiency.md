# 04 - Reliability, Security & Efficiency

## Reliability

### 1. How do you implement "Graceful Degradation"?
**Answer:**
Designing the system so that if a non-essential service fails, the main application still works. 
- **Example:** If the "Product Recommendation" service is down, the e-commerce site still allows users to browse and buy products, just without personalized suggestions.

### 2. Importance of "Health Checks" (/health)?
**Answer:**
An endpoint that returns the status of the service (e.g., UP/DOWN). Load balancers and orchestrators (Kubernetes) use these to decide where to send traffic and when to restart a service.

### 3. Role of "Structured Logging" (JSON)?
**Answer:**
Logging data in a machine-readable format (JSON) instead of plain text. 
- **Benefit:** Allows tools like **ELK Stack** or **Splunk** to easily parse, index, and alert on specific log fields.

## Security

### 4. What is the "OWASP Top 10"?
**Answer:**
A standard awareness document for developers and web application security. It lists the most critical security risks (e.g., SQL Injection, Broken Authentication, XSS, Insecure Deserialization).

### 5. How to secure an "API Key"?
**Answer:**
- Never commit it to Git. Use environment variables.
- Use a Secret Manager (AWS Secrets Manager, HashiCorp Vault).
- Implement rotation and least-privilege access.

### 6. Difference between Encryption at Rest vs. Encryption in Transit?
**Answer:**
- **At Rest:** Data stored on disk is encrypted (protects against physical theft of drives).
- **In Transit:** Data moving between client and server (or between services) is encrypted via TLS (protects against man-in-the-middle attacks).
