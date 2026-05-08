# Technical Study Guide: Senior Full Stack Engineer (Ciklum)

## 1. Python & Django Mastery
- **Django Internals:** Middlewares, QuerySet optimization (select_related/prefetch_related).
- **REST APIs:** Building robust APIs with Django REST Framework (DRF).
- **Asynchronous Python:** Using `asyncio` or Celery for background tasks (though the JD emphasizes AWS SQS/Lambda).

## 2. TypeScript & React Ecosystem
- **React Patterns:** Custom Hooks, Compound Components, and Render Props.
- **React Native:** Navigation, handling native device features, and performance differences from web.
- **State Management:** React Context vs. Redux Toolkit for complex product flows.

## 3. AWS Serverless Deep Dive
- **AWS Lambda:** Cold starts, environment variables, and execution limits.
- **DynamoDB:** NoSQL modeling (Single Table Design), Partition/Sort keys, and GSI/LSI.
- **Event-Driven Architecture:** Decoupling services using SNS and SQS.
- **API Gateway:** Request validation, Authorizers (Lambda/Cognito), and Throttling.

## 4. Cloud Infrastructure & DevOps
- **CI/CD:** Designing GitHub Actions pipelines for multi-repo deployments.
- **Infrastructure as Code (IaC):** Basics of Terraform or AWS CDK (often paired with serverless).
- **Monitoring:** CloudWatch Logs, X-Ray for tracing, and alerting strategies.

## 5. Sustainability & Supply Chain
- **Sustainability Metrics:** Understanding how software can track waste reduction and resource efficiency.
- **Data Engineering:** Collaborating with data teams to process supply chain metrics.

## 6. Architecture & Best Practices
- **SOLID Principles:** Applying them in a dynamically typed environment (Python/TS).
- **Testing:** Unit testing (Pytest/Jest), Integration testing, and E2E (Cypress/Playwright).
- **Security:** Securing S3 buckets, IAM roles (Least Privilege), and API authentication.

## Resources to Review
- [AWS Serverless Learning Path](https://aws.amazon.com/serverless/getting-started/)
- [Django Best Practices](https://django-best-practices.readthedocs.io/en/latest/)
- [React Native Documentation](https://reactnative.dev/docs/getting-started)
- [Supply Chain Sustainability (Basics)](https://www.unep.org/resources/report/sustainability-supply-chains)
