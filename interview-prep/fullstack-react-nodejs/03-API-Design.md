# RESTful API Design & Integration

**Focus:** API design, REST conventions, authentication, error handling

---

### Q1: RESTful Design Principles
**Question:** Design a RESTful API for a blog system where users can create posts and leave comments. What would the endpoints look like?

**Expected Answer:**
- **Nouns, not verbs**: URLs should represent resources, not actions.
- **Pluralization**: Standardize on plural nouns.
- **Posts**:
  - `GET /api/posts` (List posts)
  - `POST /api/posts` (Create post)
  - `GET /api/posts/:id` (Get single post)
  - `PUT /api/posts/:id` (Replace entire post)
  - `PATCH /api/posts/:id` (Update parts of a post)
  - `DELETE /api/posts/:id` (Delete post)
- **Comments (Nested Resources)**:
  - `GET /api/posts/:postId/comments` (List comments for a post)
  - `POST /api/posts/:postId/comments` (Add a comment to a post)
- **Versioning**: Include versions in the URL (e.g., `/api/v1/posts`) or headers to ensure backward compatibility when changes occur.

---

### Q2: Authentication & Authorization (JWT)
**Question:** Explain how JSON Web Tokens (JWT) work. How do you handle token expiration and secure storage on the frontend?

**Expected Answer:**
- **Mechanism**: JWT is a stateless token containing a header, payload (claims like user ID), and signature. The server verifies the signature to trust the payload without querying the database.
- **Storage**:
  - *Bad*: `localStorage` (vulnerable to XSS attacks).
  - *Good*: `httpOnly` secure cookies. JavaScript cannot access them, mitigating XSS.
- **Expiration/Refresh**:
  - Use a short-lived Access Token (e.g., 15 mins) and a long-lived Refresh Token (e.g., 7 days).
  - When the Access Token expires, the client sends the Refresh Token to a specific endpoint (`/api/auth/refresh`) to get a new Access Token.
  - Refresh Tokens should be stored securely (httpOnly cookie) and can be revoked in the database if compromised.

---

### Q3: API Error Handling
**Question:** How do you structure API error responses so that the frontend can easily consume and display them to the user?

**Expected Answer:**
- **Standard HTTP Status Codes**:
  - `200` OK, `201` Created
  - `400` Bad Request (validation errors)
  - `401` Unauthorized (not logged in)
  - `403` Forbidden (logged in, lacking permissions)
  - `404` Not Found
  - `500` Internal Server Error
- **Consistent Payload Structure**:
  ```json
  {
    "success": false,
    "error": {
      "code": "VALIDATION_FAILED",
      "message": "Invalid input data",
      "details": [
        { "field": "email", "issue": "Invalid email format" }
      ]
    }
  }
  ```
- **Never expose stack traces** to the client in production.

---

### Q4: Rate Limiting & API Security
**Question:** Your public API is being spammed, bringing down the database. How do you protect it?

**Expected Answer:**
- **Rate Limiting**: Use middleware (like `express-rate-limit`) to restrict the number of requests per IP over a time window (e.g., 100 requests per 15 minutes).
- **CORS (Cross-Origin Resource Sharing)**: Configure CORS to only allow requests from your specific frontend domains.
- **Helmet.js**: Set secure HTTP headers to protect against common web vulnerabilities (clickjacking, MIME sniffing).
- **Input Validation & Sanitization**: Use libraries like `Joi`, `Zod`, or `class-validator` (NestJS) to ensure payload schemas are strictly enforced. Prevent NoSQL/SQL injection.
- **Pagination**: Never return unbounded datasets. Always enforce limits/pagination on `GET` lists.

---

### Q5: Fetching & Integrating APIs in React
**Question:** When integrating an API into React, how do you handle loading states, caching, and background updates?

**Expected Answer:**
- **Manual approach (useEffect)**: Requires managing `data`, `loading`, and `error` state variables manually. Prone to race conditions and boilerplate.
- **Modern approach (React Query / RTK Query)**:
  - Handles caching automatically based on query keys.
  - Provides `isLoading`, `isError`, and `data` out of the box.
  - Supports background refetching (e.g., when the window regains focus).
  - Handles optimistic UI updates for mutations (updating the UI before the server responds).
