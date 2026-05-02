# 03 - Async, Multiplayer & Backends

## Networking Fundamentals

### 1. TCP vs. UDP for Games?
**Answer:**
- **TCP:** Reliable, ordered, but slow (due to handshakes and retransmissions). Good for chat, inventory, or turn-based games.
- **UDP:** Unreliable, unordered, but fast. Standard for real-time action games (FPS, Racing). If a packet is lost, you just wait for the next one rather than stalling the whole game.

### 2. What is "Client-Side Prediction" and "Reconciliation"?
**Answer:**
- **Prediction:** The client moves the player immediately without waiting for the server.
- **Reconciliation:** The server sends the "authoritative" position. If the client was wrong (e.g., hit an obstacle the client didn't know about), the client snaps back to the server's position.

### 3. How do you handle "Interpolation" for other players?
**Answer:**
Instead of snapping a remote player to their new position, you smoothly "slide" them from their last known position to the current one. This masks the jerky nature of network updates.

## Python Backends

### 4. Role of `asyncio` in a game server?
**Answer:**
Allows a single thread to handle thousands of concurrent connections (WebSockets/Sockets) without the overhead of threads. Essential for low-latency, scalable backends.

### 5. What is an "Authoritative Server"?
**Answer:**
A design where the server does all the logic (physics, collisions, damage) and the client is just a "dumb renderer." This is the best defense against cheating.

### 6. How do you implement "Lobby" and "Matchmaking" logic?
**Answer:**
- **Lobby:** A persistent service where players wait and chat.
- **Matchmaking:** A service that groups players based on skill (Elo), latency, or game mode and then spawns a dedicated game server instance for them.
