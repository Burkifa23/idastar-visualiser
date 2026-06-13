#  Iterative Deepening A* (IDA*) Visualizer

This document provides a detailed technical explanation of **Iterative Deepening A* (IDA*)** as implemented in **`idastar-visualiser.html`** for solving the Ghana Route-Finding Problem.

---

## What is Iterative Deepening A*?

**Iterative Deepening A* (IDA*)** is an informed (heuristic) search strategy that combines the linear space efficiency of **Depth-First Search (DFS)** with the optimal path-finding capabilities of **A* Search**:

1. **DFS-like Space Efficiency**: It performs a depth-first exploration, avoiding the massive memory overhead of A*'s open list (frontier).
2. **A*-like Optimality**: It uses the evaluation function:
   \[f(n) = g(n) + h(n)\]
   where:
   * g(n): actual path cost from the start node to node n.
   * h(n): straight-line distance heuristic estimate from node n to the goal.

Rather than bounding the search by edge-depth (like DLS and IDS), IDA* bounds the search by a **cost limit** (the f-bound).

---

## Step-by-Step Algorithm Mechanics

IDA* works in successive iterations, gradually increasing the f-limit threshold:

1. **Initialize Bound**: Set the initial threshold bound = f(start) = g(start) + h(start) = h(start).
2. **Depth-First Search with f-limit**:
   * Run a depth-first search starting from the root.
   * For each node traversed, compute its f(n) = g(n) + h(n).
   * If a node's f(n) exceeds the current bound, prune that branch. Record this exceeded value.
   * If the goal is found, terminate and return the path.
3. **Analyze Exceeded Thresholds**:
   * If the DFS finishes without finding the goal, find the **minimum f(n) value among all pruned nodes** that exceeded the current bound.
   * Update the bound to this minimum exceeded value (bound = min\_exceeded) and restart the DFS search from the start node.
   * If no node exceeded the bound and the goal was not found, the search space is fully explored, and no solution exists.

---

## Visualizing the Ghana Road Network with IDA*

In the visualizer, you can select a **Start City** (e.g., **Tamale**) and a **Destination City** (e.g., **Korle Bu**).

Here is a conceptual trace of how IDA* operates:
* **Initial Iteration**: The initial bound is set to the straight-line heuristic distance from **Tamale** to **Korle Bu** (bound = 600).
* **First Depth-First Search**: The search expands Tamale's neighbors. For any neighbor (e.g., Techiman, Kintampo), if the path cost g(neighbor) plus its heuristic h(neighbor) exceeds 600, the branch is pruned.
* **Updating the Bound**: Once the DFS completes, the minimum f-value that exceeded 600 is selected (e.g., 630). The bound is updated to 630, and a fresh DFS restarts from Tamale.
* **Goal Capture**: This process repeats, expanding the search bubble based on total cost until a path reaches **Korle Bu** within the current f-bound.

---

## Complexity Analysis

### 1. Space Complexity
This is the primary advantage of IDA* over standard A*. Because it runs as a depth-first stack, it does not store the entire explored set or frontier queue in memory.

The space complexity is:
\[O(b \cdot d)\]
where:
* b: average branching factor (\approx 3 to 5 in Ghana).
* d: depth of the optimal solution (number of edges).

This is a dramatic improvement over A*, which has an exponential space complexity of O(b^d).

### 2. Time Complexity
In the worst-case scenario (e.g., on graphs with many unique edge weights where the bound increments very slowly), IDA* can re-evaluate the same nodes repeatedly.

The time complexity is:
\[O(b^d)\]
However, in practice, if the heuristic is consistent and the state space is not overly dense with small unique edge costs, IDA* performs comparably to A* in terms of search speed.

---

## 🎨 Visualization State System

The visualizer shows the active execution states of IDA* in real-time:
* 🔘 **Slate Gray (`#cbd5e1`)** — Unvisited cities.
* 🔵 **Soft Blue (`#74ade8`)** — Current active DFS exploration branch.
* 🔴 **Crimson Red (`#ef4444`)** — Cost Limit Exceeded Node (node where f(n) > bound).
* 🟢 **Emerald Green (`#10b981`)** — Final optimal resolved route.
*  **Execution Logs** — Real-time tracking of node checks, f-values, and bound upgrades.
