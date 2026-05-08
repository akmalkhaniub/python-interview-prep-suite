# Technical Study Guide: Senior Full Stack Developer (.NET + React + AI)

## 1. .NET & C# Mastery
- **.NET 8 / ASP.NET Core:** Dependency Injection, Middlewares, Filter/Attribute system.
- **C# Advanced Features:** Pattern matching, async streams, records, and Span<T> for performance.
- **RESTful API Design:** Versioning, HATEOAS, and efficient DTO (Data Transfer Object) mapping.
- **EF Core:** Migration strategies, LINQ performance, and interceptors.

## 2. React & Modern Frontend
- **TypeScript:** Advanced types (Generics, Discriminated Unions, Utility Types).
- **React Performance:** Profiling, `React.memo`, and efficient state updates (Zustand or Recoil).
- **Tooling:** Vite vs. Webpack, configuring build pipelines for different platforms.
- **Accessibility (a11y):** ARIA labels, keyboard navigation, and semantic HTML.

## 3. SQL Server & Data Layers
- **Advanced SQL:** Window functions, Recursive CTEs, and JSON data handling.
- **Performance Tuning:** Reading Execution Plans, Index optimization (Clustered vs. Non-clustered), and Statistics.
- **Schema Design:** Normalization vs. Denormalization for enterprise reporting.

## 4. AI & LLM Integration (OpenAI / Gemini)
- **API Orchestration:** Handling rate limits, token counting, and retry logic (Exponential backoff).
- **Prompt Engineering:** Few-shot prompting, Chain-of-Thought (CoT), and System messages.
- **RAG (Retrieval-Augmented Generation):** Connecting SQL Server data to LLMs via Vector Databases (e.g., Azure AI Search or pgvector equivalents).
- **Azure AI / OpenAI SDKs:** Practical usage of Semantic Kernel or LangChain.

## 5. Cloud & Multi-Platform
- **Google Cloud Platform (GCP):** Cloud Run (Serverless containers), Cloud SQL (Managed SQL Server/Postgres), and IAM.
- **Microservices Architecture:** API Gateways, Service Discovery, and Eventual Consistency.
- **Source Control & CI/CD:** Advanced Git workflows, GitHub Actions/Azure DevOps pipelines.

## 6. Testing & Quality
- **Unit Testing:** xUnit/nUnit for C#, Jest/Vitest for React.
- **Integration Testing:** TestContainers for database-dependent tests.
- **E2E Testing:** Playwright or Cypress for full-stack flows.

## Resources to Review
- [.NET Documentation](https://learn.microsoft.com/en-us/dotnet/)
- [React Guide: Performance](https://react.dev/learn/render-and-commit)
- [SQL Server Performance Tuning Guide](https://learn.microsoft.com/en-us/sql/relational-databases/performance/performance-tuning-and-monitoring-tool-usage-guide)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [Google Cloud Fundamentals](https://cloud.google.com/docs/get-started)
