# Interview Q&A: Software Engineer II (NCR Atleos)

### Q1: Enterprise .NET Troubleshooting
**Question:** A high-traffic ASP.NET application on IIS is experiencing intermittent "503 Service Unavailable" errors. How do you troubleshoot this?
**Answer:** I follow a systematic approach:
1.  **Check the Event Viewer:** Look for "Application Pool Crashes." If the app pool is crashing frequently, IIS shuts it down (Rapid Fail Protection).
2.  **Inspect Resource Usage:** Is the CPU pegged? Is there a memory leak (`w3wp.exe` memory climbing)?
3.  **Audit the Logs:** Look at IIS logs for specific status codes and sub-codes (e.g., 503.2 indicates concurrent request limit reached).
4.  **Root Cause:** If it's a memory leak, I take a **Memory Dump** and analyze it using WinDbg to find the leaking object (e.g., a non-disposed `SqlConnection`).

### Q2: Distributed Systems (Hadoop/SQL)
**Question:** We need to process a dataset of 100 million transactions to find anomalies. Should we use a SQL Store Procedure or a Hadoop job?
**Answer:** It depends on the **nature of the data** and the **required latency**.
- **SQL Server:** If the data is already structured in a database and we have good indexing, a Stored Procedure might be faster for a one-off report on 100M rows.
- **Hadoop:** If the data is unstructured (e.g., raw logs from 10k ATMs) or if we need to scale to *billions* of rows, Hadoop's distributed nature is superior.
I would weigh the "ETL Overhead" of moving data into Hadoop versus the "Compute Bottleneck" of a single SQL server.

### Q3: Security (TLS 1.2)
**Question:** How do you ensure your .NET application is communicating over TLS 1.2 when calling a legacy third-party XML service?
**Answer:** 
1.  **Framework Level:** Ensure the app is targeting .NET 4.6+ (which uses TLS 1.2 by default).
2.  **Code Level:** If targeting older frameworks, explicitly set the security protocol: `ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;`.
3.  **Environment Level:** Use tools like IISCrypto to ensure TLS 1.0/1.1 are disabled on the server, forcing all communication to the modern standard.

### Q4: Quality & Best Practices
**Question:** You are asked to implement a new feature on a sub-system you are unfamiliar with. How do you ensure you maintain "Quality" while delivering on a tough deadline?
**Answer:** 
1.  **Domain Gathering:** I consult with the internal stakeholders and read existing documentation/code to understand the "Contract" of the sub-system.
2.  **Test-Driven Mindset:** I write Unit Tests for the new feature *before* implementation to define the expected behavior.
3.  **Peer Consultation:** I present my design to a senior team member early to identify any "Gaps" in my understanding before I write too much code.
4.  **Continuous Refactoring:** I leave the code cleaner than I found it, ensuring I don't add technical debt to meet the deadline.

### Q5: Software Design
**Question:** Why is "Re-use of existing software" emphasized at the Software Engineer II level?
**Answer:** Re-use accelerates delivery and improves quality because existing code is already **Tested and Proven** in production. Instead of reinventing a custom XML parser, I evaluate if our internal libraries or industry-standard ones (like Newtonsoft.Json) already solve the problem. This allows me to focus on the **Unique Business Logic** of the ATM or retail kiosks rather than utility plumbing.

---

### Pro-Tips for NCR Atleos:
- **Mention Atlanta:** If you have any US context or global experience, mention it.
- **Hardware Mindset:** Remember that your code might be running on a machine that dispenses physical money. **Accuracy is more important than "Cool" code.**
- **Enterprise Maturity:** Talk about "Root Cause Analysis" (RCA) and "Continuous Improvement." These are the buzzwords for SE II roles in large organizations.
