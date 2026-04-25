# Python Core & Concurrency
**Focus:** Python Internals, GIL, Threading vs. Multi-processing, OS Kernels, OOP

---

## Section A: Python Concurrency & OS Kernels

### Q1: The Global Interpreter Lock (GIL)
**Question:** What is the Python GIL, and how does it fundamentally impact multi-threaded applications?

**Expected Answer:**
The Global Interpreter Lock (GIL) is a mutex native to CPython that allows only one thread to execute Python bytecodes at a time. This architectural decision was made to simplify memory management and prevent race conditions when handling Python's reference-counting garbage collector. Consequently, even on a multi-core processor, a multi-threaded Python application bound by the GIL cannot achieve true parallel execution for CPU-bound tasks. While multiple threads can exist, the OS must constantly context-switch them to acquire the lock. However, the GIL is released during I/O operations (like network requests or disk reads), meaning multi-threading remains highly effective for I/O-bound workloads. To bypass the GIL entirely and achieve true parallelism for CPU-intensive tasks, engineers must leverage the `multiprocessing` module, which spawns separate OS processes, each with its own Python interpreter and memory space.

---

### Q2: Threads vs. Processes at the OS Level
**Question:** Explain the difference between threads and processes from the perspective of an Operating System kernel.

**Expected Answer:**
From an OS kernel perspective, a process is an independent execution unit that contains its own isolated memory space, file descriptors, and security context. Because processes are heavily isolated, a crash in one process typically does not affect others, but inter-process communication (IPC) requires expensive mechanisms like pipes, sockets, or shared memory segments. 

A thread, conversely, is the smallest sequence of programmed instructions that can be managed independently by the kernel scheduler. Threads exist *within* a process and share the exact same memory space and file descriptors. This shared memory allows for incredibly fast communication and lower instantiation overhead compared to processes. However, this shared memory model introduces severe risks: a bug in one thread (like a segmentation fault) will crash the entire parent process, and engineers must rigorously employ synchronization primitives (like Mutexes or Semaphores) to prevent race conditions when multiple threads mutate shared state concurrently.

---

## Section B: Python Memory & OOP

### Q3: Memory Management & Garbage Collection
**Question:** How does Python manage memory, and what happens when you encounter a memory leak in a long-running Linux process?

**Expected Answer:**
Python primarily relies on Reference Counting for memory management. Every object in Python maintains a count of references pointing to it; when this count drops to zero, the memory is immediately deallocated. However, reference counting cannot resolve "reference cycles" (e.g., Object A points to Object B, and Object B points to Object A). To handle this, Python utilizes a supplementary Generational Garbage Collector that periodically scans memory to detect and clean up isolated cyclic graphs.

In a long-running Linux production service, memory leaks often manifest as a steady increase in RAM usage until the kernel's OOM (Out Of Memory) Killer forcibly terminates the process. These leaks in Python rarely stem from true C-level memory leaks; instead, they are usually "logical leaks" where developers unintentionally maintain references to objects—such as appending data to a global list without ever clearing it, or unclosed database connections. To diagnose this, I would use tools like `tracemalloc` to snapshot memory allocations over time and identify which objects are persisting unexpectedly.

---

### Q4: Object-Oriented Programming (OOP) Principles
**Question:** How do you effectively apply OOP principles (Inheritance, Polymorphism, Encapsulation) in Python, given that Python lacks strict access modifiers?

**Expected Answer:**
Python embraces OOP but relies heavily on developer discipline and convention rather than strict compiler enforcement. **Encapsulation** is achieved by prefixing attributes with a single underscore `_` to signal they are "protected" (internal use only), or a double underscore `__` to invoke name mangling, which prevents accidental overriding in subclasses. I frequently use the `@property` decorator to expose these private variables safely, allowing me to add validation logic without breaking the API contract.

**Inheritance** is utilized to establish "is-a" relationships and promote code reuse, though I strongly favor composition over deep inheritance trees to avoid the Fragile Base Class problem. Python's support for multiple inheritance necessitates a deep understanding of the Method Resolution Order (MRO) to prevent diamond-problem bugs. **Polymorphism** in Python is naturally achieved through "Duck Typing." We don't care if an object strictly inherits from a specific base class; as long as it implements the expected methods (like `__len__` or `save()`), it can be treated uniformly, allowing for highly decoupled and flexible system architectures.
