# Data Structures, Algorithms & Prediction
**Focus:** Core CS Fundamentals, Time Complexity, Prediction Integration

---

## Section A: Data Structures in Python

### Q1: Choosing the Right Built-in Data Structure
**Question:** How do you decide between using a List, Set, or Dictionary when processing large datasets in Python?

**Expected Answer:**
Choosing the correct built-in data structure is foundational to algorithmic efficiency. A **List** is implemented as a dynamic array; it is excellent for maintaining ordered sequences and allows O(1) indexed access. However, searching for an element (`x in list`) requires an O(N) linear scan, making it terrible for membership testing on large datasets.

A **Set** is implemented using a hash table, similar to a dictionary but without values. It fundamentally guarantees uniqueness and provides O(1) average time complexity for insertions, deletions, and membership testing. I use sets exclusively when I need to quickly determine if an item exists or when performing mathematical operations like unions or intersections. A **Dictionary** (dict) is also a hash table mapping unique keys to values, offering O(1) lookups. I use dictionaries whenever data is relational or requires rapid retrieval via a unique identifier (like user ID), mapping keys to complex object payloads.

---

### Q2: Applied Data Structures
**Question:** Describe a backend engineering scenario where you would explicitly use a Queue or a Tree data structure.

**Expected Answer:**
A **Queue** (specifically a Double-Ended Queue or `collections.deque` in Python) operates on a First-In-First-Out (FIFO) principle. In backend engineering, queues are essential for managing asynchronous tasks or buffering burst traffic. For example, if a Django application needs to generate heavy PDF reports, instead of blocking the HTTP request, I would push the report request onto a message queue (like RabbitMQ or Redis). Background worker processes then pop tasks off the queue sequentially, ensuring the system isn't overwhelmed.

A **Tree** structure, particularly a Trie (Prefix Tree) or a B-Tree, is used for hierarchical data and rapid searching. If I were building an autocomplete search feature for an e-commerce site, a standard database `LIKE '%query%'` is extremely inefficient. Instead, I would load the product catalog into an in-memory Trie structure. As the user types "Lap", the backend navigates the Trie in O(M) time (where M is the prefix length) to instantly retrieve all branches containing "Laptop", providing real-time UX response times.

---

## Section B: Algorithmic Complexity & Logic

### Q3: Optimizing Time Complexity
**Question:** How do you approach optimizing an algorithm from O(N^2) to O(N) or O(N log N)? Provide an example.

**Expected Answer:**
An O(N^2) time complexity usually indicates a nested loop—comparing every element in a dataset against every other element. As data scales, O(N^2) quickly becomes computationally catastrophic. My primary strategy for reducing time complexity from O(N^2) to O(N) is introducing a Hash Map (Dictionary) to trade space complexity for time complexity.

For example, the classic "Two Sum" problem: finding two numbers in a list that add up to a target. The brute-force O(N^2) approach uses a nested loop to check every pair. To optimize this, I iterate through the list exactly once (O(N)). For each number, I calculate the `complement` needed to reach the target. I check if that `complement` exists in a hash map. If it doesn't, I store the current number in the hash map and move on. Because hash map lookups are O(1), the entire algorithm collapses from quadratic to linear time, drastically improving scalability.

---

### Q4: Graph Traversal: BFS vs. DFS
**Question:** What is the difference between Breadth-First Search (BFS) and Depth-First Search (DFS), and when would you use them?

**Expected Answer:**
BFS and DFS are fundamental graph and tree traversal algorithms. **Breadth-First Search** explores the graph level by level, moving uniformly outward from the starting node. It requires a Queue data structure to track nodes. BFS is the definitive choice when you need to find the shortest path between two nodes in an unweighted graph, such as calculating the "degrees of separation" between two users on a social network, or crawling the web one link-depth at a time.

**Depth-First Search** plunges as deep as possible down a single branch before backtracking. It relies on a Stack (or recursion). DFS is highly memory efficient compared to BFS when dealing with wide trees. I use DFS when I need to explore all possible paths to find a specific solution (like solving a maze), detecting cycles within a dependency graph (to prevent infinite loops in package managers), or when performing topological sorting for task scheduling.

---

## Section C: Prediction Algorithms

### Q5: Integrating Prediction Algorithms
**Question:** The job description mentions "prediction algorithms." How would you integrate a basic predictive model into a Django backend?

**Expected Answer:**
Integrating a prediction algorithm into a Django application requires carefully decoupling the heavy data science workloads from the synchronous web application. If the prediction is a lightweight, pre-trained heuristic (like a simple linear regression or collaborative filtering matrix), I would serialize the trained model using `joblib` or `pickle` and load it into Django's memory during application startup. The view simply passes the user's input to the model's `.predict()` method, returning the result synchronously.

However, if the prediction algorithm is complex, relies on massive datasets, or requires deep learning frameworks (like TensorFlow/PyTorch), running it within the Django process will block HTTP requests and destroy performance. In this scenario, I would architect a microservice approach. The Django app acts purely as the API gateway, sending the feature payload to a dedicated prediction microservice (often hosted on GCP Vertex AI or a dedicated container) via a gRPC or REST call, or via an asynchronous Pub/Sub queue if real-time responses aren't strictly required.
