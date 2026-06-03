# Module 02: Unblocking & Reverse Engineering

This module focuses on low-level network signatures, OS fingerprinting (p0f), de-obfuscating JavaScript challenges, and evading browser security systems.

---

## Technical Q&A

### Q1: Beyond JA3/JA4, how do advanced bot detectors use HTTP/2 settings frames and window sizes to detect automated crawlers? How do you solve this?
**Answer:**
When establishing an HTTP/2 connection, the client and server exchange **SETTINGS** and **WINDOW_UPDATE** frames. Real browsers configured on specific operating systems have highly distinct default values for these frames.

1. **HTTP/2 Fingerprinting Vectors:**
   - **SETTINGS Frame Values:** These contain parameters like `SETTINGS_MAX_CONCURRENT_STREAMS`, `SETTINGS_INITIAL_WINDOW_SIZE`, and `SETTINGS_MAX_FRAME_SIZE`.
   - **WINDOW_UPDATE Frame Increments:** The initial stream and connection window size updates.
   - **Header Frame Ordering:** The exact sequence and formatting of headers (e.g., pseudo-headers like `:method`, `:authority`, `:scheme`, `:path` must appear in a specific browser-defined order).
   - **Rejection:** If your scraper sends pseudo-headers out of order or defines custom initial window sizes that do not match the target User-Agent browser signature, you are flagged as a bot.

2. **Resolution:**
   - Modify the underlying HTTP/2 frame processor. In Node.js or Python, standard libraries do not expose these settings parameters.
   - You must compile or use customized networking stacks (like `http2-wrapper` in Node.js or `curl-impersonate` in Python/C++) which allow you to manually override the default values of the HTTP/2 SETTINGS frame and pseudo-header structures to match real browser handshakes exactly.

---

### Q2: What is TCP/IP OS Fingerprinting (e.g., p0f)? How can an anti-bot system identify a Linux worker pod that is masquerading as a Windows Chrome user?
**Answer:**
**TCP/IP OS Fingerprinting** (often implemented using tools like `p0f`) operates at the network packet level (Layer 3/4), completely bypassing the application level (Headers/JS).

1. **How it works:**
   - When a client sends a TCP SYN packet to initiate a connection, the packet headers contain parameters determined by the kernel configuration of the client OS.
   - Key attributes analyzed:
     - **TTL (Time to Live):** Default is often 64 for Linux, 128 for Windows, 255 for network routers.
     - **Window Size:** The initial TCP window size (e.g., 65535 for Windows).
     - **DF (Don't Fragment) flag:** Whether the kernel sets fragmentation bits.
     - **TCP Options:** The sequence and presence of options (e.g., MSS, SACK, Timestamp, NOP).
2. **The Mismatch Exploit:**
   - If a Python scraper runs inside a Docker container on a Linux host, its TCP SYN packets will bear a Linux signature (e.g., `TTL=64`, specific TCP options sequence).
   - If the request payload contains an HTTP header claiming `User-Agent: Windows NT 10.0; Win64`, the anti-bot firewall detects this mismatch (Linux packet signature vs. Windows User-Agent) and blocks the request at the firewall level before it ever hits the backend web application.
3. **Mitigation:**
   - Modify the Linux host kernel TCP stack variables using `sysctl` to match Windows behavior (e.g., setting `net.ipv4.ip_default_ttl = 128`).
   - Run the scraper through a residential proxy gateway. The target firewall only sees the TCP SYN packets of the residential proxy router (which is running standard consumer firmware/OS), masking the scraper's true OS kernel parameters.

---

### Q3: Describe the methodology for reverse-engineering and solving a heavily obfuscated JavaScript challenge (e.g., Akamai or Cloudflare JS challenges) to generate security cookies.
**Answer:**
Advanced anti-bot walls inject a dynamic, heavily obfuscated JavaScript script (often utilizing custom virtual machines or AST-scrambled loops) that the browser must execute to calculate a payload and exchange it for a session cookie (e.g., `cf_clearance`).

**Reverse-Engineering Methodology:**

1. **Static Analysis & De-obfuscation:**
   - Capture the script payload.
   - Use AST (Abstract Syntax Tree) manipulation tools (like Babel) to perform de-obfuscation:
     - Rename obfuscated variable names.
     - Resolve constant folding (e.g., replacing `1 + 2` with `3`).
     - Re-order control-flow flattened blocks (converting split switch statements back to sequential execution loops).
2. **Behavioral Mapping:**
   - Identify the browser checks executed by the script. Typically, it searches for DOM signatures, webdriver parameters, and canvas hash renders.
   - Note the encryption algorithms used (often custom AES or XOR variants) to generate the final verification token.
3. **Execution Emulation:**
   - **Option A (Headless execution):** Inject the stealth configuration into Playwright or Puppeteer to let the automated browser run the script naturally. This is slower but highly reliable.
   - **Option B (Node.js Sandbox):** Execute the de-obfuscated script inside a hardened Node.js sandbox environment (e.g., using `isolated-vm` to prevent access to the host system) and mock the expected DOM objects (using `JSDOM` or custom mocks). This allows the scraper to generate valid verification cookies at high speed without spawning heavy browser instances.
