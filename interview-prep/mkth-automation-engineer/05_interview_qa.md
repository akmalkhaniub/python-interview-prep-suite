# Interview Q&A: Automation Engineer (MKTH)

### Q1: Tool Selection
**Question:** We need to automate a simple Lead-Gen flow. Should we use Make.com or custom code (Node.js/Python)?
**Answer:** It depends on the **complexity** and **maintenance** requirements. 
- **Make.com** is excellent for speed and visual debugging. If we are using standard apps (Typeform -> HubSpot), Make is the right choice for rapid delivery.
- **Custom Code** is better if we have high-volume data (millions of records), complex nested logic that visual builders struggle with, or if we need to implement strict security/encryption that low-code tools don't support.
My approach is "Low-Code for Speed, High-Code for Scale."

### Q2: Troubleshooting
**Question:** A workflow that was working for months suddenly starts failing. What are your first 3 steps?
**Answer:**
1.  **Check the Execution Log:** Identify the exact module that failed and the error message (e.g., 401 Unauthorized, 429 Rate Limit, 500 Server Error).
2.  **Validate API Credentials:** Check if an API key has expired or if the service's permissions have changed.
3.  **Inspect the Input Data:** See if a recent record has a "malformed" format (e.g., a missing email field or an unexpectedly large file) that the workflow wasn't designed to handle.

### Q3: Complex Logic (N8n)
**Question:** How do you handle a scenario where an API returns an array of 100 items, and you need to perform an action for each, but only if they meet a specific criteria?
**Answer:** I use an **Iterator** to split the array into individual records. I then follow it with a **Filter** node to check the criteria. For the records that pass, I perform the action. Finally, I use an **Aggregator** if I need to collect the results of those actions into a final summary or report.

### Q4: Security & Privacy
**Question:** We are automating sensitive financial data. How do you ensure it stays secure?
**Answer:** 
1.  **Encryption:** Ensure all API calls are over HTTPS.
2.  **Least Privilege:** Use API keys that only have "Write" access to the specific folders/tables needed, not full admin access.
3.  **PII Masking:** I can implement a "Sanitization" step (like my App 16) that masks sensitive info before it's logged or sent to a 3rd party notification channel like Slack.
4.  **Self-Hosting:** If privacy is a hard requirement, I'd recommend using **N8n self-hosted** on a private server so the data never leaves our infrastructure.

### Q5: "On-Site" Proactivity
**Question:** This is an on-site role in Clifton. Why do you think being on-site helps in an Automation Engineer role?
**Answer:** Automation is about **understanding the business process**. Being on-site allows me to sit with the teams who are currently doing the work manually. I can observe their "pain points," see the edge cases they deal with, and build an automation that *actually* solves their problem, rather than just building what was written in a requirement document.

---

### Pro-Tips for MKTH:
- **Mention Clifton:** If you know the area, mention your comfort with the commute or the location.
- **Agency Mindset:** Talk about "Deliverables" and "Client Satisfaction."
- **Show, Don't Just Tell:** If you have a laptop, be ready to show a complex workflow you've built (even if it's in code).
