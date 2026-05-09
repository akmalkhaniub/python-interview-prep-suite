# Technical Study Guide: Senior Full Stack Engineer (Ciklum)

## 1. AWS Serverless Ecosystem
- **AWS Lambda:**
    - Cold starts and optimization.
    - Provisioned Concurrency.
    - Integrating with API Gateway.
- **Data Persistence:**
    - **DynamoDB:** Partition keys, sort keys, GSIs (Global Secondary Indexes), and TTL.
    - **S3:** Bucket policies, lifecycle rules, and static hosting.
- **Messaging & Event-Driven:**
    - **SNS vs. SQS:** When to use pub/sub vs. point-to-point queues.
    - **Dead Letter Queues (DLQ):** Handling failed message processing.

## 2. Python & Django Backend
- **Django REST Framework (DRF):**
    - Serializers and ViewSets.
    - Authentication (JWT, Session).
    - Permissions and Throttling.
- **Asynchronous Python:**
    - `asyncio` and `Celery` for background tasks (though SNS/SQS is preferred in serverless).
- **ORM Optimization:** Avoiding N+1 queries using `select_related` and `prefetch_related`.

## 3. Frontend & Mobile (TS/React/RN)
- **TypeScript:** Advanced types, Generics, and strict null checks.
- **React:**
    - Functional components and Hooks.
    - State management (Zustand or Redux).
- **React Native:**
    - Platform-specific code (`Platform.OS`).
    - Handling navigation and deep links.

## 4. Cloud Infrastructure & CI/CD
- **GitHub Actions:**
    - Writing `.yml` workflows for build, test, and deploy.
    - Managing secrets and environment variables.
- **Terraform / AWS SAM:** Basic understanding of Infrastructure-as-Code (IaC) for serverless resources.

## 5. Architectural Design Patterns
- **Microservices:** Service discovery and inter-service communication.
- **Cloud Patterns:**
    - **Circuit Breaker:** Preventing cascading failures.
    - **Idempotency:** Ensuring duplicate requests don't cause side effects.
- **Hexagonal Architecture:** Decoupling business logic from frameworks.

## 6. Engineering Best Practices
- **Code Reviews:** Focus on maintainability, security, and edge cases.
- **Testing:**
    - **Pytest** for backend logic.
    - **React Testing Library** for frontend components.
- **Documentation:** Using Swagger/OpenAPI for API documentation.

## Resources to Review
- [AWS Serverless Learning Path](https://aws.amazon.com/serverless/getting-started/)
- [Django REST Framework Documentation](https://www.django-rest-framework.org/)
- [React Native Performance Guide](https://reactnative.dev/docs/performance)
- [Refactoring Guru: Cloud Design Patterns](https://refactoring.guru/design-patterns/cloud)
