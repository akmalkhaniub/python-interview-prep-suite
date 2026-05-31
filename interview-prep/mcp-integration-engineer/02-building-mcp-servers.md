# Module 02: Building MCP Servers

This module focuses on practical implementation patterns for MCP servers, including schema validation, SDK usage (TypeScript/Python), and sandboxing tools.

---

## Technical Q&A

### Q1: Write a minimal, production-grade MCP Server in Python using the official `mcp` SDK. The server should register a tool called `run_query` which runs read-only SQL queries on a SQLite database. Ensure inputs are validated and errors are caught.
**Answer:**
Below is the complete implementation of the SQLite MCP server using Python:

```python
import sqlite3
from typing import List
from mcp.server.fastmcp import FastMCP, Context
from pydantic import BaseModel, Field

# Initialize FastMCP Server
mcp = FastMCP("sqlite-query-service")

# Path to database
DB_PATH = "data.db"

class QueryInput(BaseModel):
    sql: str = Field(..., description="The SELECT query to run against the SQLite database.")

@mcp.tool()
def run_query(sql: str, ctx: Context) -> str:
    """Executes a read-only SELECT query against the SQLite database and returns the results formatted as text."""
    
    # Enforce read-only constraint programmatically
    clean_query = sql.strip().lower()
    forbidden_keywords = ["insert", "update", "delete", "drop", "alter", "create", "replace"]
    if any(kw in clean_query for kw in forbidden_keywords):
        return "Error: Only SELECT queries are permitted for safety reasons."
    
    try:
        # Connect to SQLite
        conn = sqlite3.connect(DB_PATH)
        cursor = conn.cursor()
        
        # Log query execution to context for debugging (sent to client logs via stderr)
        ctx.info(f"Executing query: {sql}")
        
        cursor.execute(sql)
        rows = cursor.fetchall()
        
        # Format results
        if not rows:
            return "Query executed successfully. 0 rows returned."
        
        headers = [description[0] for description in cursor.description]
        result_lines = [" | ".join(headers)]
        result_lines.append("-" * len(result_lines[0]))
        for row in rows:
            result_lines.append(" | ".join(map(str, row)))
            
        conn.close()
        return "\n".join(result_lines)
        
    except sqlite3.Error as e:
        return f"Database Error: {str(e)}"
    except Exception as e:
        return f"Unexpected Error: {str(e)}"

if __name__ == "__main__":
    # Start server using stdio transport
    mcp.run(transport="stdio")
```

---

### Q2: How should an MCP Server handle long-running operations or high-latency external API calls? Describe how timeouts and cancellations are managed.
**Answer:**
Since MCP communication is asynchronous and JSON-RPC based, we can leverage standard async frameworks.

1. **Task Cancellation:**
   - In Python (using `asyncio`) or Node.js, asynchronous handler functions should accept execution context parameters or track standard Cancellation Tokens.
   - When a client issues a cancellation request (`$/cancelRequest`), the server SDK matches the request ID, cancels the pending coroutine, and closes open resources immediately to prevent memory leaks.

2. **Server-Side Timeouts:**
   - Do not rely on the client to time out. Wrap all database operations, HTTP requests, or subprocesses in strict timeouts using `asyncio.wait_for` (Python) or `Promise.race` (JS).
   - If a timeout occurs, return a structured error response with code `-32000` (Server error) or a specific application error string, rather than hanging the transport connection.

3. **Background Processing / SSE:**
   - If an operation takes minutes, the tool should trigger a background task and immediately return a job token.
   - The server can then expose a resource (e.g., `job://status/{id}`) that the client can query, or use Server-Sent Events to push updates to the client once the task is finished.

---

### Q3: When implementing a tool that runs arbitrary user-supplied code (e.g., a Python runner tool), how do you secure the execution environment?
**Answer:**
Running arbitrary code requested by an LLM is a high-risk vector. Standard safety patterns include:

1. **Virtualization / Isolation:**
   - **Docker Containers:** Run code inside ephemeral, network-isolated Docker containers with disk quotas and CPU limits (e.g., `--network none -m 128m --read-only`).
   - **WebAssembly (WASM):** Compile runtime engines (like Pyodide) to WebAssembly to run them in a highly restricted sandbox on the client side or host side.

2. **VFS (Virtual Filesystem) Jail:**
   - If interacting with the filesystem, do not accept raw paths. Resolve all paths relative to a "project root" and verify that the target path does not escape the jail:
     ```python
     # Python directory traversal prevention
     import os
     
     def secure_path(base_dir: str, user_path: str) -> str:
         absolute_base = os.path.abspath(base_dir)
         absolute_target = os.path.abspath(os.path.join(base_dir, user_path))
         
         # Check if target resides strictly inside the base directory
         if not absolute_target.startswith(absolute_base + os.sep) and absolute_target != absolute_base:
             raise PermissionError("Directory traversal attempt detected.")
         return absolute_target
     ```

3. **Least Privilege System Users:**
   - Run the MCP server process as a dedicated non-root user account with read/write access restricted to a specific workspace directory.
