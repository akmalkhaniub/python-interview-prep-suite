# DevOps & Release Management

**Focus:** CI/CD for .NET, release process, deployment strategies

---

### Q1: CI/CD Pipeline for .NET ERP
**Question:** Design the CI/CD pipeline for a large .NET ERP product with multiple modules.

**Expected Answer:**
- **Build**: `dotnet restore` → `dotnet build` → run analyzers.
- **Test**: Unit tests (xUnit) → integration tests (Testcontainers) → report coverage.
- **Package**: `dotnet publish` → Docker image or deployment package.
- **Stages**: `build` → `test` → `package` → `deploy-staging` → `smoke-test` → `deploy-prod` (manual gate).
- **Branch strategy**: Trunk-based development. Feature flags over long-lived branches. Release branches for hotfixes.
- **Artifact versioning**: SemVer. Auto-increment from CI. Git tag on release.
- **Mono-repo considerations**: Build only changed projects using dependency graph analysis.

---

### Q2: Release Process Management
**Question:** As Chief Architect, how do you define and manage the product release process?

**Expected Answer:**
- **Release cadence**: Regular (e.g., bi-weekly) for features. Hotfixes deployed within 24 hours.
- **Release checklist**: All tests passing, security scan clean, performance benchmarks met, release notes written, customer communication sent.
- **Feature flags**: Decouple deployment from release. Deploy to production behind flags. Enable for beta customers first.
- **Changelog**: Auto-generated from conventional commits (`feat:`, `fix:`, `breaking:`).
- **Rollback plan**: Every release has a documented rollback procedure. Blue/green deployment makes rollback instant.
- **Sign-off**: QA lead + Architect + Product Owner approve before production toggle.
- **Post-release monitoring**: Watch error rates, performance metrics for 1 hour after release.

---

### Q3: Database Deployment in CI/CD
**Question:** How do you deploy database changes alongside application code in an ERP?

**Expected Answer:**
- **EF Core Migrations**: Generated from model changes. Applied in pipeline before app deployment.
- **Alternative**: DbUp or Flyway for SQL-script-based migrations (more control for complex ERP schemas).
- **Idempotent**: Migrations must be safe to run multiple times.
- **Ordering**: Schema changes deploy FIRST. App code must work with BOTH old and new schema during transition.
- **Multi-tenant**: Migration runner iterates over all tenant databases. Parallel execution with error handling.
- **Validation**: Post-migration smoke tests — key queries, row counts, constraint checks.

---

### Q4: Containerization Strategy
**Question:** Should you containerize a .NET ERP? What are the challenges?

**Expected Answer:**
- **Benefits**: Consistent environments, scalable, cloud-agnostic deployment.
- **Challenges for ERP**:
  - Windows dependencies (COM, legacy .NET Framework libs) may require Windows containers.
  - Large images (~500MB for .NET Runtime + app). Use multi-stage builds.
  - Database migrations need to run outside the container lifecycle.
  - Session state: ERP often has session-heavy workflows. Need distributed cache (Redis).
- **Docker best practices**:
```dockerfile
# Multi-stage build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "ERP.Web.dll"]
```
- **Health checks**: `/healthz` endpoint for orchestrator (K8s/ECS) liveness probes.

---

### Q5: Environment Management
**Question:** How do you manage configuration across dev, staging, and production for a multi-tenant ERP?

**Expected Answer:**
- **ASP.NET Core config**: `appsettings.json` → `appsettings.Production.json` → Environment variables → Azure Key Vault/AWS Secrets Manager.
- **Never in code**: Connection strings, API keys, feature flags — always external.
- **Per-tenant config**: Stored in a central config database. Resolved at runtime by tenant middleware.
- **Feature flags**: LaunchDarkly, Azure App Configuration, or custom DB-backed flags.
- **Infrastructure-as-Code**: Terraform or Bicep for environment provisioning. Same template, different variable files.
- **Secret rotation**: Automated. Key Vault or Secrets Manager with rotation policies. App reloads config without restart.
