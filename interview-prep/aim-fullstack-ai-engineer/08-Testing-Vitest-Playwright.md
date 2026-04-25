# Testing: Vitest, Playwright & TDD
**Focus:** Testing Mindset, Vitest (Unit/Integration), Playwright (E2E), Flakiness

---

## Section A: Testing Strategy & Vitest

### Q1: The "Testing-First" Culture
**Question:** Describe your approach to building a "Testing-first" culture. How do you balance Unit, Integration, and End-to-End (E2E) tests?

**Expected Answer:**
A "Testing-first" culture, heavily inspired by Test-Driven Development (TDD), treats tests as executable documentation and the primary safety net against regressions, rather than an afterthought written to satisfy an arbitrary coverage metric. 

I architect testing strategy using the **Testing Pyramid**. The foundation consists of thousands of fast, highly isolated **Unit Tests** ensuring specific utility functions, complex pure algorithms (like payroll tax calculation), and individual React components behave perfectly in a vacuum. The middle layer comprises **Integration Tests**, which verify that the Node.js Controllers, Services, and real PostgreSQL database interact correctly over HTTP. At the pinnacle are a select few **End-to-End (E2E) Tests**. These are slow and brittle, so I reserve them exclusively for critical user journeys (like "User logs in, creates a shift, and generates an invoice") to ensure the entire distributed system functions harmoniously from the browser to the database.

### Q2: Unit and Integration Testing with Vitest
**Question:** How do you utilize Vitest for testing complex Node.js and TypeScript business logic?

**Expected Answer:**
I strongly prefer **Vitest** over Jest because it natively understands TypeScript and ESM out of the box, requiring zero complex configuration (like `ts-jest`), and it leverages Vite's execution pipeline for blistering fast watch-mode performance.

For Unit Testing a complex Node.js Service (e.g., `PayrollService`), I use Vitest's powerful mocking capabilities (`vi.fn()`, `vi.mock()`) to stub out external dependencies like the Database Repository or an external Stripe API call. This allows me to test complex branching logic in milliseconds without requiring a database connection. For Integration Testing, I drop the mocks. I use tools like `supertest` alongside Vitest to fire actual HTTP requests against the Express router, validating that the incoming payload successfully creates a record in a local, ephemeral test database and returns the correct HTTP 201 status code.

### Q3: Managing Test Data State
**Question:** How do you manage test data and database state when running parallel integration tests to prevent test pollution?

**Expected Answer:**
Test pollution—where Test A inserts data that inadvertently causes Test B to fail—is the leading cause of fragile test suites. When running integration tests in parallel, you absolutely cannot rely on a shared global database state.

My strategy involves strict state isolation. Before the test suite runs, I use a factory library (like Fishery or Prisma Factories) to programmatically generate highly randomized, unique test data (e.g., a unique `user_email` and `agency_id` per test block) rather than relying on hardcoded SQL seed files. Crucially, I configure the test runner to execute every individual integration test inside its own isolated SQL Transaction. During the teardown phase (`afterEach`), the test explicitly rolls back the transaction instead of committing it. This instantly drops all test-generated data, guaranteeing a pristine, isolated database state for every concurrent test without the massive overhead of dropping and recreating the schema.

---

## Section B: End-to-End Testing & Reliability

### Q4: E2E Testing Critical Journeys with Playwright
**Question:** Walk through how you use Playwright to ensure critical user journeys (like payroll submission) never regress.

**Expected Answer:**
Playwright is my preferred E2E framework because of its exceptional auto-waiting capabilities, cross-browser support, and modern TypeScript API. To test a critical journey like payroll submission, I write a script that physically drives a headless Chromium browser through the deployed staging environment.

The Playwright script automates navigating to the login page, filling in credentials, clicking the "Generate Payroll" button, interacting with complex dynamic modals, and finally verifying that the success banner appears in the DOM. Because E2E tests are notoriously slow, I run them exclusively in the CI/CD pipeline on Pull Requests targeting the `main` branch, preventing a developer from merging code that breaks the primary revenue-generating flow of the application, regardless of whether the unit tests passed.

### Q5: Eliminating "Flaky" Tests
**Question:** What causes "flaky" tests (tests that pass and fail intermittently), and how do you systematically eliminate them?

**Expected Answer:**
Flaky tests destroy developer trust in the CI/CD pipeline; if developers learn to simply "re-run the build" until it turns green, the tests have lost their value. Flakiness is almost exclusively caused by three things: race conditions, leaked global state, or reliance on third-party APIs.

In E2E testing, the most common race condition is instructing the test to click a button before the React component has fully hydrated or before an animation completes. Playwright largely mitigates this via strict auto-waiting assertions (e.g., `await expect(page.locator('button')).toBeVisible()`). For leaked state, I enforce transaction rollbacks (as mentioned previously). For third-party APIs (like a payment gateway), an E2E test will flake if the external sandbox environment has an outage. To fix this, I utilize tools like MSW (Mock Service Worker) to intercept external network requests at the browser network layer, returning deterministic, mocked JSON responses to the frontend, guaranteeing the test only validates our own internal system behavior.
