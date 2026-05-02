# 01 - API Design & Architecture

## Design Patterns

### 1. Difference between REST and GraphQL for backend services?
**Answer:**
- **REST:** Centered around resources (URLs). High standard, easy to cache at the edge, but can suffer from over-fetching or under-fetching.
- **GraphQL:** Centered around a schema and queries. Frontend requests exactly what it needs. Great for complex, nested data, but harder to cache and can lead to complex "N+1" problems on the backend if not managed properly.

### 2. How do you handle "API Versioning"?
**Answer:**
- **URL-based:** `/v1/users` (Most common and explicit).
- **Header-based:** `Accept: application/vnd.myapi.v1+json`.
- **Query Parameter:** `/users?version=1`.
- **Best Practice:** Keep versions backwards compatible as long as possible and provide clear deprecation timelines.

### 3. Importance of "Idempotency" in APIs?
**Answer:**
Ensuring that making the same request multiple times has the same effect as making it once. Crucial for POST requests (e.g., payments). 
- **Implementation:** Use an `Idempotency-Key` header stored in the DB/Cache to check if a request has already been processed.

## Reliability & Performance

### 4. How do you handle "Rate Limiting" at the API level?
**Answer:**
Preventing abuse by limiting the number of requests per user/IP. 
- **Algorithms:** Token Bucket, Leaky Bucket, or Fixed Window.
- **Tools:** Use Redis for distributed tracking and return `429 Too Many Requests` status.

### 5. What is "Pagination" and why is it mandatory?
**Answer:**
Splitting a large dataset into chunks (pages). 
- **Offset-based:** `LIMIT 10 OFFSET 20`. Easy but slow for deep pages.
- **Cursor-based:** `WHERE id > last_seen_id`. Fast and stable for real-time data, but harder to implement "jump to page X."

### 6. Importance of "Self-Documenting" APIs (OpenAPI/Swagger)?
**Answer:**
Provides a contract between backend and frontend. It allows for automatic client generation, testing, and ensures that the API implementation matches the documentation.
