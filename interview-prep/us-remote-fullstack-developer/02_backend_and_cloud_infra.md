# Deep Dive: Backend, Databases, & Cloud Infra

Modern Full-Stack developers at US startups are expected to have a "DevOps mindset."

## 1. Backend Efficiency (Node/Python)
*   **Node.js Event Loop:** Understanding non-blocking I/O and how it scales.
*   **Python (FastAPI):** Utilizing Pydantic for validation and automatic Swagger documentation.
*   **Middleware:** Implementing logging, authentication, and error handling globally.

## 2. Database Mastery
*   **PostgreSQL:**
    - Using **Migrations** to manage schema changes over time.
    - Implementing **Foreign Keys** and **Indexes** for data integrity and speed.
*   **MongoDB:**
    - Understanding the **Flexible Schema** but enforcing structure via Mongoose.
    - Handling **Deep Nesting** vs. **Referencing** (Normalization vs. Denormalization).

## 3. Containerization & Docker
*   **Why Docker?** "It works on my machine" – ensures the development, staging, and production environments are identical.
*   **Optimization:** Using multi-stage builds to keep image sizes small.
*   **Docker Compose:** Orchestrating multiple services (App + DB + Redis) for local development.

## 4. Cloud & CI/CD
*   **AWS ECS/EKS:** Running your Docker containers in the cloud.
*   **Environment Variables:** Managing secrets securely (AWS Secrets Manager or `.env` in GitHub Secrets).
*   **Deployment Pipeline:**
    - Push Code -> Run Tests -> Build Docker Image -> Push to Registry -> Deploy.

## Interview Questions
1.  "Explain the difference between a SQL and NoSQL database. When would you choose MongoDB over PostgreSQL?"
2.  "What is a 'Microservice' architecture, and what are its pros and cons?"
3.  "How do you ensure your database is secure from unauthorized access?"
4.  "What is the role of CI/CD in a modern development team?"
