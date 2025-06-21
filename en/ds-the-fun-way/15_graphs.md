# 📌 Data Structures the Fun Way - 15. Graphs

> Graphs are structures where multiple objects (nodes) are connected to each other through relationships (edges)

<br/>

## 🔖 5.1 What is a Graph?

### Graph Concept

- Graphs consist of **nodes (vertices)** and **edges**.
- Unlike other data structures, graphs weren't created to optimize specific operations, but to **directly represent relationships or flows in the real world**.

### Characteristics of Graphs

- Edges can have **directionality** or not
  - If directed: flow is determined like A → B
  - If undirected: bidirectional connection like A - B
- Edges can have **weights (costs)**
  - e.g., time or distance to go from A to B
- Complex relationships can be expressed by combining directionality and weights.

### Methods to Represent Graphs in Memory

- **Adjacency Matrix**: Represented as 2D array. Efficient when there are few nodes and many edges
- **Adjacency List**: Each node manages connected nodes as a list. Suitable for sparse graphs with few edges
- Both methods can be flexibly used depending on whether they have direction and weights.

<br/>

## 🔖 5.2. Dijkstra's Algorithm - Finding Shortest Path

### What Kind of Algorithm?

- An algorithm that finds **shortest distances from one node to all other nodes**.
- Works on **weighted graphs** and **doesn't allow negative weights**.

### How It Works

1. Create an array to store distances from start node to each node, setting all distances to infinity
2. Set start node distance to 0 and manage a set of unvisited nodes
3. Select and visit the **node with shortest distance** among unvisited nodes
4. Update distance information of neighboring nodes connected to that node as **current distance + edge weight**
5. Repeat the above process until all nodes are visited

### Summary

- Continues updating shortest distance information found so far.
- Can be efficiently implemented using **priority queues (heaps)**

<br/>

## 🔖 5.3. Prim's Algorithm - Finding Minimum Spanning Tree

### What is a Minimum Spanning Tree?

- Refers to a tree structure that **connects all nodes while minimizing the sum of all edge weights**.
- **No cycles and all nodes are connected**.

### What is Prim's Algorithm?

- A representative algorithm for finding minimum spanning trees
- Looks similar to Dijkstra's algorithm but aims for **minimum cost connections** rather than **shortest distances**.

### How It Works

1. Start from any node.
2. Select the **edge with smallest weight** that can connect to the current tree and add nodes one by one.
3. Find the cheapest edge based on the newly added node and connect it.
4. Repeat until all nodes are included in the tree.

### Summary

- Method of adding nodes that can be **connected most cheaply to the existing tree** one by one.

<br/>

## 🔖 5.4. Kahn's Algorithm - Topological Sorting

### What is Topological Sorting?

- Used when processing **tasks where order is important**.
- e.g., when one task must finish before the next task starts.
- To express this, a **directed acyclic graph (DAG)** without cycles is needed.

### What is Kahn's Algorithm?

- An algorithm that performs topological sorting in directed graphs.
- **Determines task order considering dependency relationships** between nodes.

### How It Works

1. Count **in-degree (number of incoming edges)** for all nodes.
2. Put nodes with in-degree 0 into a queue.
3. Take nodes out of the queue one by one while reducing in-degrees of connected nodes.
4. Put newly zero in-degree nodes into the queue.
5. The **order in which all nodes are taken out once** becomes the topological sorting result.

<br/>

## 🔖 5.5. Why Graphs are Important

- Graphs are structures that can **naturally express complex relationships or flows in the real world**.
- Various problems like **roads between cities, SNS friend relationships, compilation order, task scheduling, etc.** can be modeled as graphs.
- Graph algorithms are directly applied to solve real problems like **shortest paths, minimum cost connections, dependency sorting, etc.**
