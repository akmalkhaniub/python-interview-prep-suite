# Module 01: Proxy Architecture & Protocols

This module focuses on HTTP/HTTPS proxy internals, TCP tunneling mechanics, proxy chaining, and high-performance network programming.

---

## Technical Q&A

### Q1: Explain how the HTTP `CONNECT` method enables secure HTTPS tunneling through a proxy. What does the network request flow look like?
**Answer:**
When a client requests a resource via HTTPS through an HTTP proxy, the proxy cannot decrypt the encrypted SSL/TLS payload. Instead, the proxy must act as a blind TCP relay using the **HTTP CONNECT** protocol.

#### Request Flow:
1. **Connection Initiation:** The client opens a standard TCP connection to the proxy server.
2. **HTTP CONNECT Request:** The client sends a plain-text HTTP CONNECT request specifying the target host and port:
   ```http
   CONNECT api.github.com:443 HTTP/1.1
   Host: api.github.com:443
   Proxy-Connection: Keep-Alive
   ```
3. **Proxy Connection Establisher:** The proxy server opens a TCP connection to the target host (`api.github.com` on port `443`).
4. **HTTP 200 Connection Established:** If the connection succeeds, the proxy returns a plain-text status code back to the client:
   ```http
   HTTP/1.1 200 Connection Established
   ```
5. **Raw TCP Tunneling:** The proxy stops parsing HTTP headers. It treats the client socket and the target socket as a bidirectional raw pipe.
6. **TLS Handshake:** The client initiates the standard TLS handshake directly with the target host *through* the proxy tunnel. All subsequent traffic is end-to-end encrypted (E2EE) between the client and the target.

```
[Client] ──CONNECT Request──► [Proxy] ──TCP Handshake──► [Target:443]
[Client] ◄──200 established─── [Proxy] 
[Client] ◄═══════════Encrypted TLS Session═════════════► [Target:443]
```

---

### Q2: Compare SOCKS5 and HTTP/HTTPS proxies. How do they differ in terms of network layers, DNS resolution, and protocol capabilities?
**Answer:**

| Dimension | SOCKS5 Proxy | HTTP/HTTPS Proxy |
| :--- | :--- | :--- |
| **OSI Layer** | Layer 5 (Session Layer). | Layer 7 (Application Layer). |
| **Protocol Support** | Any TCP/UDP traffic (DNS, SMTP, FTP, SSH, HTTP). | Strictly HTTP, HTTPS, and WebSockets. |
| **DNS Resolution** | Can be resolved by the client (local DNS leak risk) or by the SOCKS5 server (secure). | Resolved by the proxy server natively. |
| **Authentication** | Built-in (GSS-API or Username/Password handshakes). | Handled via the `Proxy-Authorization` HTTP header. |
| **Parsing Overhead** | Extremely Low (blind bytes relay). | Higher (parses HTTP headers unless in CONNECT tunnel mode). |

**Architectural Choice:**
- Use **SOCKS5** if you need to route non-web traffic (e.g., custom database TCP connections) or require low-level UDP support.
- Use **HTTP/HTTPS** proxies for standard web scraping pipelines because they allow the injection of custom headers (e.g., `User-Agent`, cookie management) at the proxy level.

---

### Q3: Write a minimal HTTP proxy tunnel server in Node.js/TypeScript using the native `http` and `net` modules to forward HTTPS connections.
**Answer:**
Below is the complete implementation of a tunneling proxy server in Node.js:

```typescript
import http from 'http';
import net from 'net';
import { URL } from 'url';

const PORT = 8080;

const server = http.createServer((req, res) => {
    // Handle standard HTTP requests (GET, POST to non-secure endpoints)
    res.writeHead(501, { 'Content-Type': 'text/plain' });
    res.end('This proxy only supports HTTPS tunneling via CONNECT.');
});

// Intercept HTTP CONNECT tunnel requests
server.on('connect', (req: http.IncomingMessage, clientSocket: net.Socket, head: Buffer) => {
    const { port, hostname } = new URL(`http://${req.url}`);
    
    if (!hostname || !port) {
        clientSocket.write('HTTP/1.1 400 Bad Request\r\n\r\n');
        clientSocket.end();
        return;
    }

    // Connect to the target server
    const targetSocket = net.connect(Number(port), hostname, () => {
        // Send success header back to the client
        clientSocket.write('HTTP/1.1 200 Connection Established\r\n\r\n');
        
        // Write any initial buffered data that the client sent during the handshake
        if (head && head.length > 0) {
            targetSocket.write(head);
        }

        // Pipe client socket data directly to target socket, and vice versa
        clientSocket.pipe(targetSocket);
        targetSocket.pipe(clientSocket);
    });

    // Handle socket errors to prevent server crash
    targetSocket.on('error', (err) => {
        clientSocket.write('HTTP/1.1 502 Bad Gateway\r\n\r\n');
        clientSocket.end();
    });

    clientSocket.on('error', () => {
        targetSocket.end();
    });
});

server.listen(PORT, () => {
    console.log(`Proxy tunnel server running on port ${PORT}`);
});
```

---

### Q4: How do you prevent Socket Exhaustion (e.g., `EMFILE` or `EADDRINUSE` errors) when scaling a Node.js proxy server to handle 50,000 concurrent client requests?
**Answer:**
Handling high-volume concurrency in Node.js requires tuning the operating system limits and managing socket lifecycles aggressively:

1. **Raise File Descriptor Limits (OS Level):**
   - In Linux, each active TCP socket counts as a File Descriptor (FD). The default limit per process is often `1024` (resulting in `EMFILE: too many open files` errors).
   - Set the system and process limits in `/etc/security/limits.conf`:
     ```bash
     * soft nofile 65535
     * hard nofile 65535
     ```
2. **Increase local Ephemeral Port Range:**
   - When the proxy establishes outbound connections to target servers, it uses a local ephemeral port. If you run out of ports, you receive `EADDRINUSE` or `EADDRNOTAVAIL` errors.
   - Expand the default range:
     ```bash
     echo "1024 65535" > /proc/sys/net/ipv4/ip_local_port_range
     ```
3. **Configure Keep-Alive Agents:**
   - By default, Node.js HTTP clients close sockets after each request. This leaves sockets in the `TIME_WAIT` state for 60 seconds, exhausting local ports.
   - Use a persistent agent (`http.Agent` or `https.Agent` with `keepAlive: true` and `maxSockets: Infinity`) to reuse open sockets across requests.
4. **Socket Error Cleanup:**
   - Ensure all socket error and close handlers (`socket.on('error')`, `socket.on('close')`) call `socket.destroy()` to immediately reclaim VRAM and file descriptors.
