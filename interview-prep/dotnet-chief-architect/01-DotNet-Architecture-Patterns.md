# .NET Architecture & Design Patterns

**Focus:** 10+ years .NET expertise, architectural specifications, technology evaluation

---

## Section A: .NET Platform Mastery

### Q1: .NET Evolution & Migration
**Question:** You're inheriting a large codebase on .NET Framework 4.8. The business wants to modernize. Walk me through your migration strategy to .NET 8+.

**Expected Answer:**
- **Assessment**: Inventory dependencies — NuGet packages, COM interop, WCF services, Windows-specific APIs.
- **Tools**: .NET Upgrade Assistant, `try-convert`, API Compatibility Analyzer.
- **Strategy**: Strangler Fig pattern — don't rewrite everything at once.
  1. Retarget shared libraries to `.NET Standard 2.0` first (works on both).
  2. Migrate leaf services one-by-one to .NET 8.
  3. Replace WCF with gRPC or minimal APIs.
  4. Replace System.Web with ASP.NET Core middleware.
- **Risk**: EF6 → EF Core migration requires schema/query review. EDMX models don't port directly.
- **Timeline**: Large ERP = 12–18 months phased migration, running both runtimes in parallel.

---

### Q2: .NET 8 Key Features for Enterprise
**Question:** What .NET 8 features would you prioritize adopting in an ERP product?

**Expected Answer:**
- **Native AOT**: Faster startup, smaller footprint for microservices/serverless.
- **Minimal APIs**: Reduced boilerplate for internal services.
- **Blazor United (SSR + Interactive)**: Unified full-stack C# rendering model.
- **System.Text.Json Source Generators**: Compile-time serialization, no reflection.
- **TimeProvider abstraction**: Testable time-dependent code.
- **Keyed DI services**: Named service registrations without workarounds.
- **Frozen collections**: Immutable, optimized for read-heavy lookups (config data, reference tables).
- **HTTP/3 & QUIC**: Better performance for global remote users.

---

### Q3: Dependency Injection Deep Dive
**Question:** How does the built-in .NET DI container work? When would you replace it with a third-party container?

**Expected Answer:**
- **Built-in**: `IServiceCollection` + `IServiceProvider`. Supports Transient, Scoped, Singleton lifetimes.
- **Scoped in ASP.NET Core**: One instance per HTTP request. Critical for DbContext (avoid shared state).
- **Limitations**: No named registrations (until .NET 8 Keyed Services), no property injection, no child containers.
- **Replace with Autofac/Lamar when**: Need decorator pattern, module-based registration, or convention-based scanning.
- **Anti-pattern**: Service Locator — injecting `IServiceProvider` directly instead of declaring dependencies.
- **Testing**: Register mocks via `services.AddSingleton<IService>(mockInstance)` in test host.

---

### Q4: Async/Await & Thread Pool
**Question:** A junior developer writes `Task.Run(() => dbContext.SaveChanges())` in an ASP.NET Core controller. What's wrong?

**Expected Answer:**
- **Problem**: `Task.Run` offloads to thread pool — but ASP.NET Core is ALREADY on the thread pool. This wastes a thread waiting for another thread.
- **Correct**: Use `await dbContext.SaveChangesAsync()`. True async I/O doesn't block any thread.
- **Thread pool starvation**: In high-traffic ERP with 1000+ concurrent users, `Task.Run` for I/O exhausts the pool → requests queue → timeouts.
- **Rule**: `Task.Run` is for CPU-bound work (calculations, image processing). Never for I/O (DB, HTTP, file).
- **ConfigureAwait(false)**: Use in library code to avoid capturing SynchronizationContext. Not needed in ASP.NET Core (no sync context).

---

## Section B: Architectural Patterns

### Q5: Clean Architecture for ERP
**Question:** How would you structure a large ERP application using Clean Architecture?

**Expected Answer:**
```
src/
├── Domain/              # Entities, Value Objects, Domain Events, Interfaces
│   └── No dependencies on anything external
├── Application/         # Use Cases, DTOs, Validators, CQRS Commands/Queries
│   └── Depends on Domain only
├── Infrastructure/      # EF Core, External APIs, File Storage, Email
│   └── Implements Domain interfaces
├── Web/                 # ASP.NET Core, Controllers, Blazor, Middleware
│   └── Depends on Application + Infrastructure (DI composition root)
└── SharedKernel/        # Cross-cutting: Result types, pagination, audit
```
- **Dependency Rule**: Dependencies point inward. Domain has zero external dependencies.
- **CQRS**: Separate command (write) and query (read) models. Critical for ERP where reads vastly outnumber writes.
- **MediatR**: Decouple handlers from controllers. Pipeline behaviors for logging, validation, transactions.

---

### Q6: CQRS & Event Sourcing
**Question:** When would you introduce CQRS in an ERP system? Do you always need Event Sourcing with it?

**Expected Answer:**
- **CQRS without Event Sourcing**: Most common. Separate read/write models backed by the same database. Read model uses optimized queries/views.
- **When to use CQRS**: Complex domain logic (payroll calculations, budget approvals), different read/write scaling needs, audit requirements.
- **Event Sourcing**: Store every state change as an event. Replay to reconstruct state. Use for: audit trails, undo/redo, regulatory compliance.
- **ERP fit**: Financial modules (GL, AP, AR) benefit from Event Sourcing — every transaction is an immutable event.
- **Trade-offs**: Eventual consistency between read/write stores, complex replay logic, storage growth.
- **Don't over-engineer**: Simple CRUD modules (user profiles, settings) don't need CQRS.

---

### Q7: Domain-Driven Design (DDD)
**Question:** How do you apply DDD to an ERP with modules like HR, Finance, and Procurement?

**Expected Answer:**
- **Bounded Contexts**: Each module (HR, Finance, Procurement) is a bounded context with its own domain model.
- **An `Employee` in HR ≠ `Employee` in Payroll**: Different attributes, different behaviors. Don't share entities across contexts.
- **Aggregate Roots**: `PurchaseOrder` is an aggregate root containing `LineItems`. All changes go through the root.
- **Domain Events**: `OrderApproved` event published by Procurement, consumed by Finance to create a payable.
- **Anti-Corruption Layer**: When integrating with legacy modules, translate between models at the boundary.
- **Ubiquitous Language**: Use the same terms as the business (not database column names) in code.

---

### Q8: Microservices vs. Modular Monolith
**Question:** For a 45,000-user ERP product, would you recommend microservices or a modular monolith?

**Expected Answer:**
- **Modular Monolith first**: Single deployment unit, but internally organized by bounded contexts with clear module boundaries.
- **Why not microservices immediately**: ERP modules are tightly coupled (Finance needs HR data for payroll). Distributed transactions are complex. Small team overhead of managing 20+ services.
- **Modular Monolith benefits**: Simple deployment, no network latency between modules, shared database with schema-per-module.
- **Extract later**: If a module needs independent scaling (e.g., reporting), extract it to a service. Strangler Fig.
- **Communication**: In-process MediatR for module-to-module. If extracted → switch to async messaging (RabbitMQ/Azure Service Bus).
- **ERP reality**: Most successful ERPs (including SAP, Dynamics) are modular monoliths, not microservices.
