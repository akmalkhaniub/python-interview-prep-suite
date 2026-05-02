# 01 - Multi-Language Systems & Interop

## Interoperability (Interop)

### 1. How do you bridge Python and C++ for performance?
**Answer:**
- **Pybind11:** A lightweight header-only library that exposes C++ types in Python and vice versa. It's the modern standard for ML libraries (like PyTorch).
- **Ctypes:** A built-in Python library for calling functions in shared libraries (DLLs/SOs). Requires more manual memory management.
- **Cython:** A language that is a superset of Python, allowing you to call C/C++ functions directly and get C-like performance.

### 2. What is JNI (Java Native Interface)?
**Answer:**
A programming framework that allows Java code running in a JVM to call and be called by native applications (C/C++).
- **Use Case:** When a Java backend needs to use a high-performance ML library written in C++.

### 3. Explain Go's "CGO."
**Answer:**
A mechanism that allows Go packages to call C code. 
- **Tradeoff:** It increases build complexity and adds overhead to function calls (due to stack switching), so it should be used only when necessary.

## Memory & Concurrency

### 4. How do you handle "Memory Leaks" in a multi-language system?
**Answer:**
- **Ownership:** Clearly define which language owns a piece of memory.
- **RAII:** Use Resource Acquisition Is Initialization in C++ to ensure memory is freed.
- **Reference Counting:** Be careful when passing objects between Python (Ref counted) and C++ (Manual/Smart pointers).
- **Tools:** Valgrind (C++), VisualVM (Java), Pprof (Go), and memory_profiler (Python).

### 5. Challenges of "Concurrency" across languages?
**Answer:**
- **Global Interpreter Lock (GIL):** Python's limitation.
- **Thread Safety:** Ensuring that C++ code called from Go or Java is thread-safe.
- **Signals:** Handling OS signals (like Ctrl+C) consistently across the bridge.

### 6. What is "FFI" (Foreign Function Interface)?
**Answer:**
A general term for the mechanism by which a program written in one language can call routines or make use of services written in another.
