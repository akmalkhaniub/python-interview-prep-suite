# Technical Leadership & Mentoring

**Focus:** Mentoring developers, senior management interaction, platform technical expert, remote leadership

---

## Section A: Mentoring & Developer Growth

### Q1: Mentoring Junior-to-Mid Developers
**Question:** A mid-level developer consistently writes code that works but is poorly structured. How do you address this without demotivating them?

**Expected Answer:**
- **Private conversation first**: "I've noticed some patterns in your PRs. Can we pair on the next feature?"
- **Show, don't tell**: Pair program on a real task. Refactor their code together, explaining WHY.
- **Specific feedback**: "This 200-line method does 5 things. Let's extract each responsibility." Not: "Your code is messy."
- **Incremental improvement**: Don't rewrite their PR. Suggest 2-3 improvements per review. They'll learn over time.
- **Learning resources**: Share specific articles/videos on the pattern they need. Not a generic "read Clean Code."
- **Celebrate progress**: When they apply feedback in the next PR, acknowledge it publicly.
- **Written standards**: Point to the team's coding standards document so it's not "your opinion" but "our standard."

---

### Q2: Knowledge Sharing in Remote Teams
**Question:** How do you share architectural knowledge across a 100% remote, globally distributed team?

**Expected Answer:**
- **Architecture docs**: Living documentation in the repo (ADRs, architecture diagrams, module guides).
- **Tech talks**: Monthly "Lunch & Learn" — a developer presents a deep dive. Recorded for async viewing.
- **RFC process**: Major changes require a written proposal. Comments from the whole team before implementation.
- **Code walkthroughs**: New module? Record a 30-min video walking through the architecture. Store in wiki.
- **Pair programming rotations**: Pair across modules. Reduces knowledge silos.
- **Decision log**: Searchable history of "why did we choose X over Y" — invaluable for new hires.
- **Office hours**: Weekly open slot. Any developer can bring questions. No agenda required.

---

### Q3: Conducting Architecture Reviews
**Question:** A team proposes a new module design. How do you conduct the architecture review?

**Expected Answer:**
- **Pre-review**: Developer submits design document covering: problem statement, proposed solution, alternatives, trade-offs, data model, API contracts, security considerations.
- **Review meeting**: Walk through the design. Focus on:
  - Does it align with our bounded context boundaries?
  - Can it scale to our largest customer (45,000 users)?
  - Is it testable? Are dependencies injectable?
  - Security: authentication, authorization, data validation.
  - Performance: identify potential bottlenecks.
- **Output**: Approved / Approved with changes / Needs redesign. Written summary with action items.
- **Follow-up**: Check implementation matches the approved design. Not "design it and forget."

---

## Section B: Interacting with Senior Management

### Q4: Communicating Technical Decisions to Non-Technical Stakeholders
**Question:** The CEO asks: "Why can't we just add this feature in 2 weeks?" You know it requires a 3-month architectural change. How do you respond?

**Expected Answer:**
- **Acknowledge the business need**: "I understand this feature would unlock $X revenue. Let me explain our options."
- **Use analogies**: "It's like adding a floor to a building. The feature itself is the room — but we need to reinforce the foundation first."
- **Present options with trade-offs**:
  1. **Quick hack (2 weeks)**: Feature works but creates tech debt. Will slow down next 3 features.
  2. **Proper solution (3 months)**: Sustainable. Enables 5 future features easily.
  3. **Phased approach (6 weeks)**: Deliver MVP in 4 weeks with proper foundation. Full feature in 6 weeks.
- **Recommend**: Option 3 with a clear timeline and milestones.
- **Never say "no"**: Say "yes, and here's what it takes."

---

### Q5: Roadmap Input
**Question:** Product wants to add AI-powered features to the ERP. How do you provide technical input to the roadmap?

**Expected Answer:**
- **Feasibility assessment**: What's possible today vs. what needs R&D. Prototype key ideas in 1-2 week spikes.
- **Technical prerequisites**: "Before AI features, we need a data pipeline and a clean, normalized dataset."
- **Build vs. Buy**: Use Azure OpenAI/AWS Bedrock APIs for LLM features. Don't build your own models.
- **Use cases for ERP**:
  - Smart categorization of expenses
  - Anomaly detection in financial data
  - Natural language report queries
  - Auto-fill forms from scanned documents (OCR + LLM extraction)
- **Risk**: AI outputs need human review in financial/HR contexts. Never auto-approve based on AI alone.
- **Timeline**: Present as phases — data foundation (Q1), first AI feature (Q2), expansion (Q3-Q4).

---

## Section C: Behavioral & Leadership Questions

### Q6: Handling Disagreements with Senior Engineers
**Question:** A senior developer with 15 years of experience disagrees with your architectural direction. How do you handle it?

**Expected Answer:**
- **Listen first**: They may have context you don't. "Help me understand your concern."
- **Data over authority**: "Let's evaluate both approaches against our criteria: scalability, maintainability, team capability."
- **Prototype both**: If time permits, a 2-day spike on each approach settles debates with evidence.
- **Disagree and commit**: If you must decide, explain your reasoning, acknowledge their points, and commit together.
- **Never pull rank**: "I'm the architect so we do it my way" destroys trust.
- **Follow up**: If their concern proves valid later, acknowledge it. Adapt the approach.

---

### Q7: Building Culture in a Remote Team
**Question:** How do you maintain technical excellence and team culture in a fully remote, async-first environment?

**Expected Answer:**
- **Async-first**: Important decisions documented in writing (RFCs, ADRs), not decided in ephemeral Slack messages.
- **Over-communicate**: Status updates, decision rationale, meeting notes — always written.
- **Pair programming**: Not just for coding. Pair on architecture reviews, debugging, incident response.
- **Recognition**: Public shoutouts for great PRs, innovative solutions, helping teammates.
- **1:1s**: Weekly or bi-weekly. Mix career growth with technical mentoring.
- **Team rituals**: Code review showcase (share best/worst code seen that week), retrospectives, blameless post-mortems.
- **Trust**: Measure output, not hours online. Flexible hours mean respecting timezone differences.

---

### Q8: STAR Format Preparation

Prepare answers for:
1. **Complex Problem**: "Tell me about the most complex technical problem you solved."
2. **Mentoring Impact**: "Describe how you elevated a team's engineering practices."
3. **Stakeholder Management**: "How did you convince leadership to invest in technical improvements?"
4. **Technology Decision**: "Describe a technology choice you made that had significant business impact."
5. **Conflict Resolution**: "Tell me about a time you had to navigate conflicting technical opinions."
6. **Failure & Learning**: "What's the biggest architectural mistake you've made and what did you learn?"

**STAR Format:**
- **S**ituation: Product, team size, constraints.
- **T**ask: Your specific role and responsibility.
- **A**ction: What YOU did (use "I", not "we").
- **R**esult: Quantify impact ("Reduced deployment failures by 70%", "Enabled team to ship 2x faster").
