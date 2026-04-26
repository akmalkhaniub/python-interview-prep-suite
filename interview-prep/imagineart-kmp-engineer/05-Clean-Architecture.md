# 05: Clean Architecture for KMP

Clean Architecture helps in organizing the code such that it is independent of UI, Database, and external frameworks.

## 1. Layers in KMP
- **Domain Layer (commonMain):** 
    - Entities (Plain Kotlin data classes).
    - Use Cases / Interactors.
    - Repository Interfaces.
- **Data Layer (commonMain/androidMain/iosMain):**
    - Repository Implementations.
    - Data Sources (API, Database).
    - Mappers (Entity <-> DTO).
- **Presentation Layer (commonMain + CMP):**
    - ViewModels.
    - MVI State/Intent definitions.
    - Composable UI.

## 2. Dependency Injection (Koin)
Koin is the preferred DI for KMP because it doesn't rely on code generation (unlike Hilt/Dagger), making it easier to set up across platforms.

```kotlin
// commonMain
val commonModule = module {
    single<UserRepository> { UserRepositoryImpl(get()) }
    factory { GetUserUseCase(get()) }
    viewModel { UserViewModel(get()) }
}
```

## 3. Product Mindset: Ownership & Iteration
The JD mentions "problem → architecture → implementation → release → iteration".
- **Problem:** Understand the *why* before the *how*.
- **Architecture:** Choose patterns that allow for change.
- **Release:** Use Feature Flags to rollout safely.
- **Iteration:** Monitor Firebase/Sentry for issues and user feedback.

## 4. Potential Interview Questions
1. **How do you structure a shared module to keep it "Clean"?**
   - *Answer:* Keep the Domain layer purely Kotlin-based. Interfaces are defined in Domain, and implementations in Data. Use DI to inject implementations into the Domain layer's Use Cases.
2. **Where should platform-specific logic reside?**
   - *Answer:* If it's a utility (like a logger), use `expect`/`actual`. If it's a heavy integration (like Apple Pay), define an interface in `commonMain` and implement it in `iosMain`.
3. **What are the benefits of using Use Cases in a small team?**
   - *Answer:* Even in small teams, Use Cases provide a clear entry point for business logic, making it easier for new developers to understand the app's capabilities without digging into ViewModels or Repositories.
