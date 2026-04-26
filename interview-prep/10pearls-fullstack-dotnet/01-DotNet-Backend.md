# 01: Modern .NET & Backend Architecture

10Pearls emphasizes scalability, security, and maintainability in their .NET applications.

## 1. .NET 8+ Key Features
- **Minimal APIs:** Lightweight way to build HTTP services.
- **Native AOT:** Compiling to native code for faster startup and smaller memory footprint.
- **Enhanced Dependency Injection:** Keyed services and better performance.

## 2. Clean Architecture (The "Onion" Architecture)
- **Core Layer:** Entities, Interfaces, Domain logic.
- **Application Layer:** Use Cases, DTOs, Mapping.
- **Infrastructure Layer:** Database context, External API clients, File storage.
- **Web Layer (UI):** Controllers, Web API, Middleware.
*Rule: Inner layers should never depend on outer layers.*

## 3. C# Best Practices
- **Records & Structs:** Using `record` for immutable data objects.
- **Pattern Matching:** Modern switch statements and property patterns.
- **LINQ:** Efficient querying, avoiding "N+1" problems (using `.Include()` in EF Core).

## 4. Web API Best Practices
- **Middleware:** Custom logging, Global Exception Handling.
- **Filters:** Authorization, Validation filters.
- **Versioning:** Using `Asp.Versioning.Http` to manage API versions.

## 5. Potential Interview Questions
1. **Explain the difference between `AddSingleton`, `AddScoped`, and `AddTransient` in .NET DI.**
   - *Answer:* **Singleton:** One instance for the app's lifetime. **Scoped:** One instance per web request. **Transient:** New instance every time it's requested.
2. **What is "Clean Architecture" and why is it useful?**
   - *Answer:* It separates the business logic from technology concerns. It makes the app testable, independent of the UI, and independent of the database.
3. **How do you handle large file uploads in .NET Web API?**
   - *Answer:* Use **Streaming** instead of reading the whole file into memory. Configure `MultipartReader` and increase the request body size limit if necessary.
4. **What are "Minimal APIs" and when should you use them over Controllers?**
   - *Answer:* Minimal APIs are for small, microservice-based endpoints with minimal overhead. Controllers are better for large, complex APIs where grouping by resource is needed.
