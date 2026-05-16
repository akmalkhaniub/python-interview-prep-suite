# .NET Enterprise & IIS Deep-Dive

NCR Atleos relies on a robust .NET/IIS stack. This guide covers the enterprise nuances of these technologies.

---

## 1. ASP.NET & C# Lifecycle
*   **Request Pipeline:** Understanding the IIS pipeline -> HTTPModules -> HTTPHandlers -> ASP.NET Page/Controller lifecycle.
*   **Asynchronous Programming:** Using `async/await` and `Task Parallel Library (TPL)` to prevent thread starvation in high-concurrency enterprise apps.
*   **Generic Collections & LINQ:** Expert use of `List<T>`, `Dictionary<T, K>`, and LINQ for efficient data manipulation in memory.

## 2. ADO.NET & Database Patterns
*   **ADO.NET vs. ORM:** While Entity Framework is common, NCR Atleos mentions ADO.NET. This means you need to be comfortable with:
    *   `SqlDataReader` for fast, forward-only data reading.
    *   `SqlDataAdapter` and `DataSet/DataTable` for disconnected data scenarios (legacy apps).
    *   **Stored Procedures:** Moving complex logic to the database for performance and security.
*   **Database Tuning:** Using Execution Plans in SQL Server/Oracle to identify missing indexes or poorly performing joins.

## 3. IIS 7.0+ Administration
*   **Application Pools:** Understanding isolation, recycling policies, and identity (Managed Service Accounts).
*   **Binding & SSL/TLS:** 
    *   **TLS 1.2:** The JD explicitly mentions this. It requires configuring registry keys on the server and ensuring the .NET application is targeting a framework (4.5+) that supports it natively.
*   **Logging:** Using IIS Advanced Logging to capture custom headers or performance metrics for troubleshooting.

## 4. Web Services & REST
*   **XML Web Services (SOAP/WSDL):** Still common in financial enterprises for rigid service contracts.
*   **RESTful APIs:** Using ASP.NET Web API or Minimal APIs to build modern, lightweight endpoints.
*   **Talking Point:** "I bridge the gap between legacy SOAP services and modern RESTful APIs, ensuring that old kiosk hardware can still communicate with new cloud-native backends."

## 5. C++ Interop (Niche requirement)
*   **P/Invoke:** Using `DllImport` to call low-level C++ libraries (common for hardware interaction like card readers or cash dispensers) from C#.
*   **Unmanaged Code:** Handling memory management (pointers) when interacting with legacy C++ subsystems.
