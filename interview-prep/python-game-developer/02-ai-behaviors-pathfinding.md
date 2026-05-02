# 02 - AI Behaviors & Pathfinding

## AI Patterns

### 1. Finite State Machines (FSM) vs. Behavior Trees?
**Answer:**
- **FSM:** Good for simple AI with distinct states (Idle, Chase, Attack). Can become "spaghetti" as complexity grows.
- **Behavior Trees (BT):** Hierarchical structure. Easier to design complex, modular behaviors. Uses Nodes (Sequence, Selector, Condition, Action). More standard in modern game AI.

### 2. What are "Steering Behaviors"?
**Answer:**
Simple mathematical rules to simulate complex movement (developed by Craig Reynolds).
- **Seek:** Move toward a target.
- **Flee:** Move away from a target.
- **Arrive:** Slow down as you approach.
- **Wander:** Move randomly but smoothly.
- **Flocking:** Separation, Alignment, Cohesion.

### 3. How do you implement a simple "Vision" system for an NPC?
**Answer:**
Using **Raycasting** or **Cones of Vision**. Check the distance to the player and the angle relative to the NPC's forward vector. If within range/angle and no obstacles are in the way (Line of Sight), the player is "seen."

## Pathfinding

### 4. Explain the A* (A-Star) Pathfinding algorithm.
**Answer:**
Finds the shortest path from start to goal. It uses a heuristic ($h$) to estimate the distance to the goal and the actual cost ($g$) from the start.
- **Formula:** $f(n) = g(n) + h(n)$.
- It maintains an "Open List" of nodes to explore and a "Closed List" of visited nodes.

### 5. What is a "NavMesh" (Navigation Mesh)?
**Answer:**
A collection of 2D polygons that define the walkable areas of a 3D environment. Pathfinding on a NavMesh is much more efficient than on a dense grid because it has fewer nodes.

### 6. How do you handle "Obstacle Avoidance" in real-time?
**Answer:**
- **Local Avoidance:** Using sensors or rays to detect immediate obstacles and adjust the velocity vector.
- **Reciprocal Velocity Obstacles (RVO):** A more advanced method where agents coordinate to avoid each other without oscillating.
