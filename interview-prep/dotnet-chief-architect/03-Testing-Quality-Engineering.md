# Testing & Quality Engineering

**Focus:** Unit/integration tests, backend and frontend testing, quality mindset

---

## Section A: Testing Strategy

### Q1: Testing Pyramid for ERP
**Question:** How do you structure the testing strategy for a large ERP product with 10+ modules?

**Expected Answer:**
- **Testing Pyramid**: Many unit tests (fast, isolated) → fewer integration tests (DB, API) → fewer E2E tests (Playwright/Selenium).
- **Unit tests**: Domain logic, validators, calculations (payroll, tax, budget). No I/O. Mocked dependencies.
- **Integration tests**: API endpoints with real DB (in-memory SQLite or Testcontainers for PostgreSQL/SQL Server). Test EF queries.
- **E2E tests**: Critical user flows only — login, create PO, approve, generate report. Expensive to maintain.
- **Contract tests**: If modules communicate via APIs, use Pact to verify contracts between consumer and provider.
- **Coverage target**: 80%+ for Domain/Application layers. Don't chase 100% — test behavior, not implementation.

---

### Q2: Unit Testing Best Practices
**Question:** Show me how you'd unit test a complex payroll calculation service.

**Expected Answer:**
```csharp
public class PayrollCalculatorTests
{
    private readonly PayrollCalculator _calculator;
    
    public PayrollCalculatorTests()
    {
        _calculator = new PayrollCalculator(
            new FakeTaxRateProvider(),    // No real DB
            new FakeDeductionRepository() // Deterministic data
        );
    }

    [Theory]
    [InlineData(5000, 0.20, 1000)]   // 20% tax bracket
    [InlineData(10000, 0.30, 3000)]  // 30% tax bracket
    [InlineData(0, 0.20, 0)]         // Edge case: zero salary
    public void CalculateTax_ReturnsCorrectAmount(
        decimal grossSalary, decimal taxRate, decimal expectedTax)
    {
        var result = _calculator.CalculateTax(grossSalary, taxRate);
        Assert.Equal(expectedTax, result);
    }

    [Fact]
    public void CalculateTax_NegativeSalary_ThrowsException()
    {
        Assert.Throws<ArgumentException>(() => 
            _calculator.CalculateTax(-100, 0.20m));
    }
}
```
- **AAA pattern**: Arrange, Act, Assert.
- **Theory + InlineData**: Test multiple cases without code duplication.
- **Edge cases**: Zero, negative, max values, null inputs.
- **Naming**: `Method_Scenario_ExpectedResult`.

---

### Q3: Integration Testing with ASP.NET Core
**Question:** How do you test API endpoints with a real database in CI?

**Expected Answer:**
```csharp
public class PurchaseOrderApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public PurchaseOrderApiTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.WithWebHostBuilder(builder => {
            builder.ConfigureServices(services => {
                // Replace real DB with test DB
                services.RemoveAll<DbContextOptions<AppDbContext>>();
                services.AddDbContext<AppDbContext>(options =>
                    options.UseInMemoryDatabase("TestDb"));
            });
        }).CreateClient();
    }

    [Fact]
    public async Task CreatePO_ValidData_Returns201()
    {
        var po = new { VendorId = 1, Amount = 5000 };
        var response = await _client.PostAsJsonAsync("/api/purchase-orders", po);
        
        Assert.Equal(HttpStatusCode.Created, response.StatusCode);
    }
}
```
- **WebApplicationFactory**: Boots the entire app in-memory. Real middleware pipeline.
- **Testcontainers** (preferred over InMemory): Spin up real SQL Server/PostgreSQL in Docker for CI. Catches DB-specific bugs.
- **Seeding**: Reset DB between tests. Use transactions that roll back.

---

## Section B: Quality Engineering

### Q4: Code Quality Metrics
**Question:** As Chief Architect, what quality metrics do you track and enforce?

**Expected Answer:**
- **Code coverage**: Minimum 80% for business logic layers. Displayed in CI pipeline.
- **Cyclomatic complexity**: Flag methods with complexity > 10. Enforce via analyzers.
- **Maintainability index**: Track via SonarQube/NDepend.
- **Technical debt ratio**: SonarQube measures time-to-fix all code smells vs. total development time.
- **Duplication**: < 3% duplicated lines.
- **Dependency metrics**: Fan-out (how many things a class depends on). High fan-out = fragile.
- **Build time**: Track CI pipeline duration. Alert if > 15 min.

---

### Q5: Static Analysis & Linting
**Question:** What tools do you use to enforce code quality automatically?

**Expected Answer:**
- **Roslyn Analyzers**: Built into the compiler. Custom rules for naming, null safety, async patterns.
- **StyleCop.Analyzers**: Enforce consistent code style (braces, spacing, ordering).
- **SonarQube/SonarCloud**: Security vulnerabilities, code smells, tech debt tracking.
- **NDepend**: Architectural rules ("Domain layer must not reference Infrastructure"), dependency graphs.
- **.editorconfig**: Shared formatting rules across the team (indent, line endings, using directives).
- **Enforce in CI**: Treat warnings as errors for analyzers. Block PR if SonarQube quality gate fails.

---

### Q6: Mutation Testing
**Question:** What is mutation testing and how does it go beyond code coverage?

**Expected Answer:**
- **Problem with coverage**: 100% coverage doesn't mean tests catch bugs. You can cover every line without asserting anything meaningful.
- **Mutation testing**: Automatically modifies your code (mutants) — changes `>` to `<`, removes `if` conditions, replaces `true` with `false`. Then runs your tests. If tests still pass → your tests are weak.
- **Tool for .NET**: Stryker.NET.
- **Mutation score**: % of mutants killed by tests. Target: > 70% for critical business logic.
- **Use selectively**: Run on Domain/Application layers, not on UI or generated code. It's slow.
