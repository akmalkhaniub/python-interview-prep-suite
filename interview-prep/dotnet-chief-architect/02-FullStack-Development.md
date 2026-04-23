# Full-Stack .NET Development

**Focus:** 5+ years full-stack, ASP.NET Core, Blazor, frontend frameworks, APIs

---

## Section A: Backend (ASP.NET Core)

### Q1: API Design Philosophy
**Question:** You're designing APIs for an ERP system consumed by both a web frontend and third-party integrations. What patterns do you follow?

**Expected Answer:**
- **RESTful for external**: Standard HTTP verbs, proper status codes, versioning (`/api/v2/`), HATEOAS for discoverability.
- **CQRS endpoints for internal**: `POST /api/commands/approve-purchase-order` (command), `GET /api/queries/purchase-orders?status=pending` (query).
- **Versioning strategy**: URL path (`/v1/`) for breaking changes. Header-based for minor versions.
- **Pagination**: Cursor-based for large datasets (not offset — offset skips rows on every query).
- **Bulk operations**: `POST /api/batch` for ERP imports (payroll upload, budget imports).
- **API documentation**: OpenAPI/Swagger with `Swashbuckle` or `NSwag`. Auto-generate client SDKs.

---

### Q2: Entity Framework Core Best Practices
**Question:** What EF Core pitfalls have you seen in large codebases and how do you prevent them?

**Expected Answer:**
- **N+1 queries**: Lazy loading loads related data one-by-one. Fix: eager load with `.Include()` or use projection (`.Select()`).
- **Tracking overhead**: Use `.AsNoTracking()` for read-only queries. Significant perf improvement in reporting.
- **Migration conflicts**: In a team, migrations can conflict. Use a naming convention and squash migrations periodically.
- **Large contexts**: Split into multiple `DbContext` per bounded context, not one god context.
- **Raw SQL when needed**: `FromSqlRaw()` or Dapper for complex reporting queries. Don't force everything through LINQ.
- **Connection pooling**: EF Core reuses connections by default. But watch for connection leaks in long-running operations.
- **Compiled queries**: `EF.CompileQuery()` for hot-path queries to skip expression tree compilation.

---

### Q3: Middleware Pipeline
**Question:** Explain the ASP.NET Core middleware pipeline. How would you add custom global error handling and request logging?

**Expected Answer:**
- **Pipeline order matters**: Each middleware calls `next()` to pass to the next. Order: Exception Handling → HTTPS → Static Files → Routing → Auth → Endpoints.
- **Global error handling**:
```csharp
app.UseExceptionHandler(errorApp => {
    errorApp.Run(async context => {
        var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;
        // Log, map to ProblemDetails, return structured error
    });
});
```
- **Request logging**: Custom middleware that logs request path, duration, status code. Use `Stopwatch` + `ILogger`.
- **Problem Details (RFC 7807)**: Standardized error responses with `type`, `title`, `status`, `detail`.

---

## Section B: Frontend

### Q4: Blazor for ERP
**Question:** Would you choose Blazor Server, Blazor WebAssembly, or Blazor United for an ERP frontend? Why?

**Expected Answer:**
- **Blazor Server**: Thin client, all logic on server via SignalR. Good for internal tools. Issue: latency-sensitive, connection-dependent.
- **Blazor WASM**: Runs in browser. Offline-capable. Issue: large initial download (~5-10MB), limited debugging.
- **Blazor United (.NET 8)**: Best of both — SSR for initial load, interactive components where needed. Progressive enhancement.
- **ERP choice**: **Blazor United** — SSR for fast initial page loads (dashboards, reports), interactive for forms (data entry, approvals).
- **Component library**: MudBlazor or Radzen for enterprise-grade UI components (data grids, charts, dialogs).
- **State management**: Cascading parameters for simple state, Fluxor for complex (Redux-like for Blazor).

---

### Q5: JavaScript/TypeScript Interop
**Question:** The ERP has existing JavaScript libraries (charting, PDF generation). How do you integrate them with Blazor?

**Expected Answer:**
- **JS Interop**: `IJSRuntime.InvokeAsync<T>("functionName", args)` to call JS from C#.
- **JS → .NET**: `DotNetObjectReference` to pass a C# object to JS for callbacks.
- **Isolation**: Use JS modules (`import()`) per component, not global scripts.
- **Performance**: Minimize interop calls — batch data, don't call JS in a loop.
- **Alternative**: If the team has React/Angular expertise, use Blazor for backend pages and React for complex interactive modules (embedded via iframe or micro-frontend).

---

### Q6: Frontend Testing
**Question:** How do you test a Blazor frontend?

**Expected Answer:**
- **Unit tests**: bUnit — render components in-memory, assert on markup, simulate events.
```csharp
[Fact]
public void Button_Click_Updates_Counter() {
    var cut = RenderComponent<Counter>();
    cut.Find("button").Click();
    cut.Find("p").MarkupMatches("<p>Current count: 1</p>");
}
```
- **Integration tests**: `WebApplicationFactory` + Playwright for full browser testing.
- **Snapshot testing**: bUnit supports verifying rendered HTML against saved snapshots.
- **Accessibility**: axe-core integration in Playwright tests for WCAG compliance.
- **Visual regression**: Percy or Chromatic for catching unintended UI changes.
