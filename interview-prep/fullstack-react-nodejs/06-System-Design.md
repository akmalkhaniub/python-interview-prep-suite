# System Design & Ownership

**Focus:** Feature ownership, scalability, clean code, translating business requirements

---

### Q1: Feature Ownership (End-to-End)
**Question:** The JD emphasizes taking ownership of features from design to deployment. Walk me through how you would handle a requirement to "Add a user notification system" from start to finish.

**Expected Answer:**
- **1. Requirements Gathering**: Clarify with PM/Designers. (Real-time vs Email? Read/Unread status? Scale?).
- **2. System Design**:
  - *DB Schema*: Create a `Notifications` table (userId, message, type, isRead, createdAt).
  - *Backend*: Create endpoints to fetch and mark as read. Set up WebSockets (Socket.io) or SSE for real-time delivery if required.
- **3. Frontend Design**: Create a generic Bell Icon component. Update state using Context or React Query.
- **4. Development & Testing**: Write unit tests for the backend logic and integration tests for the API.
- **5. Code Review**: Open a PR, explain architectural decisions, address peer feedback.
- **6. Deployment & Monitoring**: Ensure DB migrations run safely. Monitor logs post-deployment to ensure the WebSocket connections aren't overwhelming the server.

---

### Q2: Translating Business Requirements
**Question:** A PM asks you to make the application "load faster" because users are complaining. How do you translate this vague requirement into technical action items?

**Expected Answer:**
- **Quantify**: I would first ask for metrics or set up monitoring (Google Lighthouse, Datadog) to define "slow" (e.g., TTFB, LCP).
- **Audit Backend**: Check if database queries are missing indexes or suffering from the N+1 problem. Check if API payloads are excessively large.
- **Audit Frontend**: Check bundle sizes. Implement lazy loading for routes/images. Ensure React components aren't re-rendering unnecessarily.
- **Propose Solutions**: Create Jira tickets for specific technical fixes (e.g., "Add Redis caching for homepage data," "Paginate user list API," "Implement React.lazy for settings page").
- **Measure**: Deploy changes and compare metrics against the baseline.

---

### Q3: Scalability
**Question:** Your Node.js/React application becomes wildly successful overnight. Traffic increases 100x. What parts of the stack break first, and how do you scale them?

**Expected Answer:**
- **Node.js API**: Since it's single-threaded, CPU will max out.
  - *Fix*: Scale horizontally. Run multiple instances behind a Load Balancer (e.g., using AWS ALB or Kubernetes HPA). Ensure the API is completely stateless (no local sessions).
- **Database**: Connection limits will be hit, and reads will slow down.
  - *Fix*: Implement connection pooling. Add Read Replicas and route `GET` requests to them. Add indexes. Add a caching layer (Redis) for frequently accessed, rarely changing data.
- **Frontend Assets**: Server bandwidth will max out serving JS/CSS.
  - *Fix*: Ensure frontend is hosted on a CDN (CloudFront) to serve static assets from edge locations globally.

---

### Q4: Clean Code & Maintainability
**Question:** You inherit a legacy Node.js codebase where a single file has 3,000 lines of spaghetti code. How do you refactor it to ensure maintainability without breaking existing features?

**Expected Answer:**
- **Don't rewrite immediately**: Resist the urge to delete and rewrite from scratch.
- **Add Tests**: Write integration tests around the existing functionality to create a safety net.
- **Identify Boundaries**: Look for logical domains (e.g., User logic, Payment logic).
- **Extract Methods**: Move large chunks of logic into smaller, well-named helper functions.
- **Extract Modules**: Move those helper functions into separate files/classes (e.g., Controllers, Services, Data Access Layer).
- **Iterative PRs**: Make small, incremental refactoring PRs rather than one massive rewrite PR.

---

### Q5: Debugging Production Issues
**Question:** A user reports that they click a "Submit" button and nothing happens, but it works fine on your local machine. How do you debug this?

**Expected Answer:**
- **Gather Info**: Ask for OS, Browser, steps to reproduce, and check if it's reproducible by other users.
- **Logs**: Check frontend error tracking (e.g., Sentry) for unhandled JS exceptions. Check backend logs (Datadog/CloudWatch) for 500 errors around that timestamp.
- **Network**: Ask the user (or check logs) to see if the API request is failing (e.g., a CORS issue, or a 400 Bad Request).
- **Data State**: The issue might be specific to their user data state in the production database (e.g., a missing field that exists locally).
