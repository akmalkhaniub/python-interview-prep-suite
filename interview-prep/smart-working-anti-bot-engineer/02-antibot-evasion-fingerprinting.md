# Module 02: Anti-Bot Evasion & Fingerprinting

This module focuses on HTTP/TLS internals, browser fingerprinting, headless stealth techniques, and defeating modern bot protection networks (Cloudflare, Akamai).

---

## Technical Q&A

### Q1: What are JA3 and JA4 TLS fingerprints, and how do modern anti-bot systems use them to block non-browser requests? How do you bypass this in Python?
**Answer:**
Even if a scraper mimics a browser's headers exactly, the underlying networking library (like Python's `requests` or `urllib`) establishes the initial TLS handshake differently than a real browser.

1. **How JA3/JA4 Works:**
   - During the TLS client hello handshake, the client sends lists of supported cipher suites, extensions, elliptic curve curves, and point formats.
   - **JA3** hashes these parameters into a MD5 fingerprint string (e.g., `771,4865-4866...,10-11...,0`).
   - **JA4** is a more modern, readable representation divided into three sections: `JA4_ClientType_TransportProtocol_CipherSuiteLength_ExtensionLength`.
   - Anti-bot systems (like Cloudflare) maintain databases of real browser JA3/JA4 fingerprints. If a request claims to be Chrome in the `User-Agent` header, but its TLS handshake yields a JA3 fingerprint characteristic of Python's `urllib3` or `Go-http-client`, the request is instantly blocked.

2. **How to Bypass JA3/JA4 in Python:**
   - Traditional Python libraries use the system's default OpenSSL bindings, which cannot be modified dynamically.
   - To bypass this, we must use HTTP clients compiled with custom SSL sockets that spoof a real browser's client hello handshake:
     - **curl-impersonate:** A modified build of `curl` that mimics the TLS/HTTP2 handshakes of Chrome/Firefox.
     - **curl_cffi:** A Python binding wrapper around `curl-impersonate`.
     - **HTTPX + TLS Client:** Using specialized Python libraries (like `tls_client`) that wrap a Go-based client hello engine.

**Implementation Example:**
```python
from curl_cffi import requests

# Spoofing Chrome 120 TLS handshake and headers automatically
response = requests.get(
    "https://ja3er.com/json",
    impersonate="chrome120", # Emulates the TLS JA3 fingerprint of Chrome 120
    headers={"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"}
)

print(response.json())
```

---

### Q2: What is Browser Fingerprinting, and what specific attributes are checked by advanced systems (like Akamai or Datadome) to detect headless automation?
**Answer:**
Browser fingerprinting collects client-side execution parameters via Javascript to build an identifier and verify user authenticity. Key vectors include:

1. **Canvas Fingerprinting:**
   - The browser is instructed to draw a hidden, complex shape or text onto an HTML5 `<canvas>` element using specific fonts and colors.
   - Because different hardware (GPUs, screen resolutions, OS font renderers) uses different anti-aliasing and rasterization algorithms, the generated image data produces a unique hash that identifies the device configuration.
2. **WebGL Fingerprinting:**
   - Queries the system's graphics driver capabilities, GPU vendor, and renderer strings, or renders a small 3D shape to analyze texture compression and shaders.
3. **Execution Environment Checks:**
   - **`navigator.webdriver`:** Headless Chrome natively sets this boolean to `true`. Evasion requires overriding this variable to `false` or `undefined`.
   - **Chrome Runtime Check:** Real Chrome has a `window.chrome` object. Headless environments often lack this or have missing attributes.
   - **Permissions API:** Check if `navigator.permissions.query` returns inconsistent results compared to a real user.
   - **User-Agent vs. Platform:** Check if `navigator.platform` matches the User-Agent string (e.g., claiming to be macOS but having a platform string of `Linux x86_64`).

---

### Q3: How do you build a "stealth" configuration for Headless Playwright/Puppeteer to bypass canvas, WebGL, and webdriver checks?
**Answer:**
To make automated browsers look human, developers use stealth plugins or override runtime JS properties during the initialization phase before any page script executes.

**Implementation Pattern using Playwright Python:**
```python
import asyncio
from playwright.async_api import async_playwright

async def run():
    async with async_playwright() as p:
        # Launch browser in headful mode or use a custom user data directory
        browser = await p.chromium.launch(
            headless=True,
            args=[
                "--disable-blink-features=AutomationControlled", # Prevents navigator.webdriver = true
                "--use-fake-ui-for-media-stream",
            ]
        )
        
        context = await browser.new_context(
            user_agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
            viewport={"width": 1920, "height": 1080},
            device_scale_factor=1,
            is_mobile=False,
            has_touch=False,
            locale="en-US",
            timezone_id="America/New_York"
        )
        
        # Inject JavaScript to override browser fingerprint variables before loading pages
        await context.add_init_script("""
            // Override navigator.webdriver
            Object.defineProperty(navigator, 'webdriver', {
                get: () => undefined
            });

            // Spoof window.chrome
            window.chrome = {
                runtime: {},
                loadTimes: function() {},
                csi: function() {}
            };

            // Spoof plugins list (headless browsers often have length 0)
            Object.defineProperty(navigator, 'plugins', {
                get: () => [1, 2, 3, 4, 5]
            });
        """)
        
        page = await context.new_page()
        await page.goto("https://bot.sannysoft.com/")
        # Take screenshot to verify check marks
        await page.screenshot(path="stealth_check.png")
        await browser.close()

asyncio.run(run())
```

---

### Q4: Explain the architectural integration of CAPTCHA solvers (e.g., 2Captcha, CapSolver) in automated scraping pipelines. How do you bypass reCAPTCHA v3 or Cloudflare Turnstile asynchronously?
**Answer:**
CAPTCHA bypass should be designed asynchronously so it does not block the primary scraping worker thread.

1. **reCAPTCHA v3 / Turnstile Mechanics:**
   - These systems do not show puzzles. Instead, they run background analytics to rate the user's interaction behavior (reCAPTCHA v3 returns a score from `0.0` to `1.0` where `0.0` is bot).
   - If the score is low, the page script intercepts submit calls.

2. **Integration Steps for Solver APIs (Token-Based Evasion):**
   - **Step 1: Extract site key.** Locate the site key (`data-sitekey`) from the target page DOM.
   - **Step 2: Request solution.** Send an asynchronous POST request containing the site key and target page URL to the solver API. The API returns a `request_id`.
   - **Step 3: Poll for token.** Periodically poll the solver API (e.g., every 5 seconds) until a human worker or machine learning solver completes the challenge and returns a **token string**.
   - **Step 4: Inject token.** Inject the token into the target page's hidden inputs (usually `#g-recaptcha-response` or `[name="cf-turnstile-response"]`) using Javascript:
     ```javascript
     document.getElementById("g-recaptcha-response").innerHTML = "SOLVER_TOKEN_STRING";
     ```
   - **Step 5: Trigger form submit.** Call the callback function registered on the target form to process the submission.
3. **Workflow Scaling:**
   - Do not solve CAPTCHAs inline. Run a worker queue.
   - If a page presents a puzzle, capture the payload, push the job to an async solver queue, pause the worker thread, and let the worker pull the solved token once it's populated, maximizing system throughput.
