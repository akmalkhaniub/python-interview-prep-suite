# 03: Microservices & Azure DevOps

10Pearls uses distributed systems to scale Global 1000 enterprise applications.

## 1. Microservices Architecture
- **Service Discovery:** How services find each other.
- **API Gateway (Azure API Management):** The single entry point for clients.
- **Circuit Breaker (Polly):** Preventing a single service failure from bringing down the entire system.

## 2. Azure Service Bus
- **Queues:** One-to-one messaging (Point-to-point).
- **Topics & Subscriptions:** One-to-many messaging (Pub/Sub).
- **Dead-letter Queues:** Handling messages that cannot be processed.

## 3. Containerization
- **Docker:** Creating immutable images of services.
- **Kubernetes (AKS):** Orchestrating containers (scaling, self-healing, rolling updates).

## 4. Azure DevOps (CI/CD)
- **YAML Pipelines:** Version-controlled build and release definitions.
- **Artifacts:** Storing NuGet packages or build outputs.
- **Infrastructure as Code (Bicep/ARM):** Deploying Azure resources via code.

## 5. Potential Interview Questions
1. **What is the "Outbox Pattern" in Microservices?**
   - *Answer:* It ensures atomicity between database updates and message publishing. You save the message to an "Outbox" table in the same transaction as the data update, and a separate process picks it up and sends it to the Service Bus.
2. **Explain the difference between Docker and a Virtual Machine.**
   - *Answer:* VMs virtualize hardware (slow, large). Docker virtualizes the OS kernel (fast, lightweight), allowing apps to run in isolated containers.
3. **How do you manage configuration in a Microservices environment?**
   - *Answer:* Use **Azure App Configuration** or Key Vault. Centralize settings so they can be changed without redeploying every service.
4. **What is "Blue-Green Deployment"?**
   - *Answer:* Having two identical production environments. One is live (Blue), and you deploy the new version to the other (Green). Once Green is tested, you flip the switch. This allows for zero downtime and instant rollbacks.
