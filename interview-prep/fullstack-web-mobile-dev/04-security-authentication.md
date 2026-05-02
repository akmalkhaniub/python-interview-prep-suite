# 04 - Security & Authentication

## Authentication (AuthN)

### 1. How does JWT (JSON Web Token) authentication work?
**Answer:**
1. User logs in with credentials.
2. Server validates and signs a token (containing user payload) with a secret key.
3. Token is sent to the client.
4. Client stores it (localStorage or Cookies) and sends it in the `Authorization` header for subsequent requests.
5. Server verifies the signature without needing to query the DB.

### 2. Difference between "Session" and "Token-based" authentication?
**Answer:**
- **Session:** Stateful. Server stores session data in memory/DB. Requires a `Session ID` cookie. Harder to scale horizontally.
- **Token:** Stateless. All data is in the token. Easier to scale but tokens cannot be easily revoked before they expire.

### 3. What is OAuth2?
**Answer:**
An authorization framework that allows a user to grant a third-party application access to their information on another site (e.g., "Login with Google") without sharing their password.

## Authorization (AuthZ) & Protection

### 4. What is RBAC (Role-Based Access Control)?
**Answer:**
Assigning permissions to roles (e.g., Admin, Editor, Viewer) and then assigning users to those roles. 

### 5. How to prevent SQL Injection?
**Answer:**
Use **Prepared Statements** or **Parameterized Queries**. Never concatenate user input directly into a query string. ORMs (like Prisma or Sequelize) usually do this by default.

### 6. What is "Cross-Site Scripting" (XSS) and how to prevent it?
**Answer:**
When an attacker injects malicious scripts into a trusted website. 
- **Prevention:** Sanitize user input, use Content Security Policy (CSP) headers, and use frameworks like React that automatically escape content.
