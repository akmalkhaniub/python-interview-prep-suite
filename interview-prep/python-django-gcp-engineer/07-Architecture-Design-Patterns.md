# Design Patterns & Architecture
**Focus:** OOP Design Patterns, Web Architecture, System Coupling

---

## Section A: Core Design Patterns in Python

### Q1: Singleton and Factory Patterns
**Question:** Explain the Singleton and Factory design patterns. How and when do you use them in Python?

**Expected Answer:**
The **Singleton** pattern ensures that a class has only one single instance globally, providing a centralized point of access. While true Singletons are somewhat controversial in Python (often replaced by simple module-level variables), they are useful for managing shared, expensive resources like a database connection pool or a centralized configuration manager. In Python, this is typically implemented by overriding the `__new__` dunder method to return the existing instance if it has already been created, rather than allocating new memory.

The **Factory** pattern is a creational pattern that abstracts the instantiation logic of objects. Instead of calling a class constructor directly throughout the codebase, the client calls a Factory method, passing a parameter (like a string). The Factory determines which specific subclass to instantiate and return. This is heavily used when building extensible systems, such as a notification service. A `NotificationFactory` can return an `EmailNotifier`, `SMSNotifier`, or `PushNotifier` depending on the user's preference, allowing engineers to add new notification types without modifying the core business logic.

---

### Q2: The Strategy Pattern
**Question:** How does the Strategy pattern help manage varying business logic, and how would you implement it?

**Expected Answer:**
The Strategy pattern is a behavioral pattern used to define a family of algorithms, encapsulate each one, and make them interchangeable at runtime. It solves the anti-pattern of massive `if/elif/else` blocks handling different variations of business logic within a single function. 

For example, consider an e-commerce checkout system calculating shipping costs. Instead of embedding logic for FedEx, UPS, and USPS directly into the `Order` class, I would define a common `ShippingStrategy` interface. I then create concrete classes: `FedExStrategy`, `UPSStrategy`, etc., each implementing their specific cost calculation algorithms. The `Order` class simply holds a reference to a `ShippingStrategy` object and calls `calculate_cost()`. This adheres strictly to the Open/Closed Principle: if we add DHL shipping tomorrow, we simply create a new `DHLStrategy` class without ever touching the existing `Order` code.

---

### Q3: The Observer Pattern & Django Signals
**Question:** Describe the Observer pattern. How does Django's Signal framework implement this?

**Expected Answer:**
The Observer pattern establishes a one-to-many dependency between objects. When the "Subject" changes state, it automatically notifies all registered "Observers," allowing for highly decoupled, event-driven architectures. The Subject doesn't need to know anything about the Observers; it just broadcasts the event.

Django's Signal dispatcher is a native implementation of the Observer pattern. For instance, when a `User` model is created (the Subject), we might need to send a welcome email, create a Stripe customer profile, and initialize user preferences. Hardcoding these three actions into the `User.save()` method tightly couples disparate domains. Instead, we emit a `post_save` signal. The email service, billing service, and preferences service all register as Observers (receivers) listening for that specific signal. When the user is saved, Django notifies them all, executing their independent logic while keeping the core `User` model pristine and decoupled.

---

## Section B: Web Application Architecture

### Q4: High Cohesion and Loose Coupling
**Question:** A core tenet of software architecture is "High Cohesion and Loose Coupling." What does this mean in the context of a web application?

**Expected Answer:**
**High Cohesion** means that the code within a specific module or service is strictly related and focused on a single, well-defined purpose. For example, an `Authentication` module should exclusively handle login, registration, and token validation. If it starts handling user profile picture resizing, it suffers from low cohesion. Highly cohesive modules are easier to maintain, test, and understand.

**Loose Coupling** dictates that different modules should be as independent as possible, interacting only through strictly defined, stable interfaces (like APIs or message queues). If the `Billing` service must directly access the `Authentication` database tables to function, they are tightly coupled. A change in the auth schema will break billing. By achieving High Cohesion and Loose Coupling, you create a resilient architecture where developers can refactor, deploy, or entirely rewrite individual components without triggering cascading failures across the wider system.

---

### Q5: The Repository Pattern
**Question:** What is the Repository pattern, and why might you use it to abstract data access in a framework like Django?

**Expected Answer:**
The Repository pattern acts as an abstraction layer between the domain business logic and the data mapping layer (the ORM or database). Instead of writing database queries directly inside business logic functions, the logic calls a "Repository" interface (e.g., `UserRepository.get_active_users()`). The repository handles the actual ORM querying and returns clean domain objects.

While the Django ORM itself is essentially an implementation of the Active Record pattern, injecting raw `.objects.filter()` calls throughout views and services couples the entire application deeply to Django's specific ORM syntax. If you ever need to change underlying databases, implement complex caching, or move data retrieval to an external microservice, you would have to rewrite hundreds of queries. By introducing the Repository pattern, the business logic remains completely ignorant of *how* the data is fetched. To add Redis caching to user retrieval, you only modify the `UserRepository` class, leaving the business logic untouched, significantly improving testability and architectural flexibility.
