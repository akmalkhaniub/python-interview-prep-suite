# 01 - Game Logic & Simulation Systems

## Core Architecture

### 1. What is the "Game Loop" in Python?
**Answer:**
The central heart of a game that repeatedly executes until the game ends.
- **Initialize:** Setup assets/state.
- **Input:** Handle user events.
- **Update:** Apply logic, move objects, check collisions.
- **Render:** Draw objects to the screen.
- **Timing:** Ensuring the loop runs at a consistent "Frame Rate" (FPS).

### 2. Explain the "Entity-Component System" (ECS) pattern.
**Answer:**
A architectural pattern that favors composition over inheritance.
- **Entity:** A unique ID (e.g., a Player or an NPC).
- **Component:** Data-only structures (e.g., `Position`, `Velocity`, `Health`).
- **System:** Logic that processes entities with specific components (e.g., `MovementSystem` updates `Position` using `Velocity`).
- **Benefit:** Highly modular and cache-friendly.

### 3. How do you handle "State Management" in a complex simulation?
**Answer:**
Using a **Finite State Machine (FSM)** or a **State Pattern**. This ensures the simulation is always in a valid, predictable state (e.g., `Loading` -> `Menu` -> `Playing` -> `Paused`).

## Simulation Logic

### 4. What is "Delta Time" (dt) and why is it crucial?
**Answer:**
The time elapsed since the last frame. Multiplying movement by `dt` (e.g., `pos += speed * dt`) ensures that objects move at the same speed regardless of the frame rate (framerate independence).

### 5. How do you achieve "Determinism" in a Python simulation?
**Answer:**
Ensuring that the same inputs always result in the same outputs.
- Use fixed-point math or consistent rounding.
- Avoid floating-point non-determinism across different OS/CPUs if possible.
- Use a seed for all random number generation.
- Update logic at a fixed time step (Fixed Update).

### 6. Importance of "Object Pooling"?
**Answer:**
Instead of constantly creating (`instantiating`) and destroying (`garbage collecting`) objects like bullets or particles, you pre-create a "pool" of objects and reuse them. This prevents performance spikes caused by Python's GC.
