# AI Red Teaming & Guardrails Security Engineer

## About the job
**Location: London, UK / Remote**

### About the Company
We build mission-critical enterprise workflows powered by LLM agent systems. Our customers trust our agents with deep access to files, transactional APIs, database queries, and private emails. Because our agents execute tools autonomously, preventing adversarial instruction execution, security exploits, and prompt injection attacks is our highest technical priority.

### Role Overview
We are looking for an **AI Red Teaming & Guardrails Security Engineer** to join our AI Security & Trust team. In this role, you will perform adversarial attacks (red teaming) on our internal agent systems to identify security weaknesses. You will design, build, and deploy runtime guardrail filters, dual-LLM check mechanisms, input/output validation layers, and secure, isolated execution sandboxes to ensure our agents remain safe, compliant, and resilient against malicious user and data inputs.

### Key Responsibilities
- **Adversarial Red Teaming:** Design and execute structured adversarial attacks (e.g., direct prompt injection, indirect prompt injection, data poisoning, jailbreaking) on agent systems.
- **Guardrail Implementation:** Implement and manage robust guardrail engines (such as NeMo Guardrails, Llama Guard, or custom Pydantic-based filters) to sanitize LLM inputs and validate outputs.
- **Secure Sandboxing:** Architect secure WebAssembly (WASM), gRPC, or micro-virtualized (Firecracker/Docker) environments for running untrusted LLM-generated code.
- **Security Reviews:** Analyze API schema designs, agent tool interfaces, and database queries to ensure there are no privilege escalation or insecure direct object reference (IDOR) vectors.
- **Compliance & Monitoring:** Build real-time anomaly detection pipelines to monitor LLM token footprints, suspicious tool execution sequences, and possible data exfiltration attempts.

### Must-Have Qualifications
- **Application Security Background:** 5+ years of experience in AppSec, pentesting, red teaming, or secure systems engineering.
- **AI Security Experience:** 2+ years of practical experience working with LLM vulnerabilities, OWASP Top 10 for LLMs, and guardrail architectures.
- **Sandboxing & OS Hardening:** Strong understanding of Linux namespace isolation, containerization, and memory-safe runtimes (e.g., WASM/Rust).
- **Strong Language Fundamentals:** High proficiency in **Python** and **Go** or **TypeScript**.
- **Security Tooling:** Experience with security scanners, static analysis tools, and web protocol analysis (TCP/IP, TLS, HTTP security headers).

### Nice-to-Have Qualifications
- Certified Information Systems Security Professional (CISSP), OSCE, or equivalent certifications.
- Experience building automated adversarial fuzzing tools for LLM endpoints.
- Active participation in the AI security open-source community or security research disclosures.
