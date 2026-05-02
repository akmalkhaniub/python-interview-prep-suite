# 02 - Cloud Backend & Frameworks

## Framework Deep-Dives

### 1. Spring (Java) vs. Django (Python) vs. Gin (Go)?
**Answer:**
- **Spring Boot:** Enterprise-grade, heavy use of Dependency Injection (DI) and AOP. Excellent for complex, microservices-heavy systems.
- **Django:** "Batteries included." Fast development, built-in ORM and Admin. Best for data-driven Python backends.
- **Gin/Echo (Go):** Lightweight, extremely fast, and great for building small, high-performance microservices or sidecars.

### 2. How do you handle "Dependency Injection" in large systems?
**Answer:**
- **Spring:** Using `@Autowired` and `@Component` annotations.
- **Go:** Manual injection in the `main.go` or using libraries like `Wire` or `Dig`.
- **Python:** Often manual, or using libraries like `Dependency-Injector`.
- **Goal:** To decouple components, making them easier to test and swap.

### 3. What is an "ORM" (Object-Relational Mapper) and its pros/cons?
**Answer:**
- **Pros:** Fast development, database agnostic code, security (SQLi prevention).
- **Cons:** Performance overhead, "N+1" query problems, abstraction leakage (you still need to know SQL for optimization).

## Cloud Architecture

### 4. How do you design for "High Availability" (HA)?
**Answer:**
- **Redundancy:** Multiple instances of services across Availability Zones.
- **Load Balancing:** Distributing traffic.
- **Health Checks:** Automatically removing unhealthy instances.
- **Database Replication:** Using Read Replicas and Failover.

### 5. What is "Statelessness" in Cloud Backends?
**Answer:**
The design principle where a service doesn't store any client state between requests. This allows any instance of the service to handle any request, making scaling and recovery much easier.

### 6. Role of "Caching" (Redis) in AI Backends?
**Answer:**
- Storing frequent model predictions.
- Managing session state across instances.
- Rate limiting and distributed locking.
