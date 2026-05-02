# 05 - Performance Optimization in Python

## Identifying Bottlenecks

### 1. How do you profile a Python game?
**Answer:**
- **cProfile:** For high-level function timing.
- **line_profiler:** To see which specific lines are slow.
- **memory_profiler:** To detect memory leaks or high usage.
- **Custom Timers:** Using `time.perf_counter()` around specific loops.

### 2. What is the "GIL" (Global Interpreter Lock) and how does it affect games?
**Answer:**
A mutex that protects access to Python objects, preventing multiple native threads from executing Python bytecodes at once.
- **Impact:** Multi-threading in Python won't speed up CPU-bound tasks (like physics).
- **Fix:** Use **Multiprocessing** for independent tasks or move heavy logic to **C/C++ extensions**.

### 3. Importance of "Numpy" or "Vectorization" in simulations?
**Answer:**
Replacing a standard Python `for` loop with a Numpy array operation. Numpy is written in C and can perform operations on thousands of elements in a single CPU instruction (SIMD), which is orders of magnitude faster.

## Optimization Techniques

### 4. When and how to use Cython or Pybind11?
**Answer:**
When a specific algorithm (e.g., pathfinding, collision detection) is too slow in pure Python.
- **Cython:** Allows writing C-like code in a Python-like syntax.
- **Pybind11:** For bridging existing C++ libraries to Python.

### 5. How to optimize memory usage in Python?
**Answer:**
- Use **Slots** (`__slots__`) in classes to prevent the creation of `__dict__`, saving significant RAM for millions of objects.
- Use **Generators** instead of large lists.
- Manually trigger `gc.collect()` in non-critical times (like loading screens).

### 6. What is "Spatial Partitioning" (Quadtrees/Octrees)?
**Answer:**
A way to divide the game world into a hierarchy of regions. Instead of checking collisions between every object ($O(n^2)$), you only check objects in the same or neighboring regions ($O(n \log n)$).
