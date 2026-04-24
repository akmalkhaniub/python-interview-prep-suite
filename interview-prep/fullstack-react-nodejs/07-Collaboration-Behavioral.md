# Collaboration, Code Reviews & Behavioral

**Focus:** Independent work, teamwork, code quality standards, problem-solving attitude

---

### Q1: Conducting Code Reviews
**Question:** The job description states you'll actively participate in code reviews. What do you look for when reviewing a colleague's Pull Request?

**Expected Answer:**
- **Functionality**: Does the code solve the problem or implement the feature described in the ticket?
- **Readability & Clean Code**: Are variables named well? Are functions small and focused? Is the code easy to understand for the next developer?
- **Architecture**: Does it follow the established project patterns (e.g., placing business logic in services, not controllers/components)?
- **Performance & Security**: Are there any obvious N+1 queries? Is user input sanitized? Are React hooks causing infinite render loops?
- **Testing**: Did they include unit/integration tests for the new logic?
- **Tone**: I always ensure my comments are constructive, asking questions ("What do you think about extracting this?") rather than making demands ("Fix this").

---

### Q2: Receiving Feedback
**Question:** Tell me about a time you received critical feedback on a Code Review that you initially disagreed with. How did you handle it?

**Expected Answer:**
- **STAR Method**:
  - **Situation**: A senior developer left a comment on my PR suggesting I rewrite a complex data transformation using a different library rather than my custom logic.
  - **Task**: I needed to resolve the comment to get the PR merged, but I felt my custom logic was faster.
  - **Action**: I didn't argue in the PR comments. Instead, I set up a quick 10-minute call to understand their perspective. They explained that while my code was fast, the library was already used across the codebase and was battle-tested for edge cases I hadn't considered.
  - **Result**: I realized consistency and safety were more important than a micro-optimization in this context. I happily refactored the code to use the library.

---

### Q3: Working Independently vs. Teamwork
**Question:** This role requires you to "manage workload effectively with minimal supervision," but also "collaborate closely with designers and product managers." How do you balance deep, independent work with constant collaboration?

**Expected Answer:**
- **Communication first**: I start my day by checking in on Slack, reviewing PRs, and answering questions.
- **Time Blocking**: I block out chunks of time (e.g., 2-3 hours) on my calendar as "Focus Time" where I mute non-urgent notifications to do deep coding work.
- **Proactive Updates**: To avoid micro-management, I proactively over-communicate my status. I leave detailed notes on Jira tickets and bring up blockers immediately during daily standups.
- **Cross-functional empathy**: When talking to designers or PMs, I avoid technical jargon and focus on user impact and business value.

---

### Q4: Continuous Learning
**Question:** The JavaScript ecosystem moves incredibly fast. How do you stay updated with emerging technologies and decide when to incorporate them into a project?

**Expected Answer:**
- **Staying Updated**: I follow industry newsletters (e.g., JavaScript Weekly), listen to podcasts, and follow key developers on Twitter/GitHub.
- **Evaluation**: I don't adopt shiny new tools just because they are new. I evaluate them based on:
  - Does it solve a real pain point we currently have?
  - What is the learning curve for the team?
  - Is the community around it large and actively maintained?
- **Implementation**: I usually propose testing a new tool (like switching from Webpack to Vite) in a small, non-critical side project or a Hackathon day before suggesting a migration for the main production app.

---

### Q5: Handling Deadlines and Pressure
**Question:** Tell me about a time you realized you were not going to meet a deadline for a critical feature. What did you do?

**Expected Answer:**
- **STAR Method**:
  - **Situation**: I was leading the development of a new payment gateway integration, and we discovered a massive undocumented issue in the third-party API just days before launch.
  - **Task**: I had to manage the fallout and adjust the delivery.
  - **Action**: I immediately flagged the issue to the Product Manager and Engineering Manager. I didn't just bring the problem; I brought options. Option A: Delay the launch by a week. Option B: Launch on time but only support Credit Cards, delaying PayPal support to a fast-follow release.
  - **Result**: The business chose Option B. We met the launch date with core functionality, and I delivered the rest the following week. Transparency and providing options built trust with the stakeholders.
