---
title: CS61b 22. Tree Traversals and Graphs
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/22
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [CS 61B Spring 2023](https://sp23.datastructur.es/index.html) | [Ch 22. Tree Traversals and Graphs](https://cs61b-2.gitbook.io/cs61b-textbook/22.-tree-traversals-and-graphs) |[[video (2022)](https://www.youtube.com/watch?v=wkkCVWn7au4&list=PL8FaHk7qbOD4tIQrwqsx16fNq6uXNhauw)] [[slides](https://docs.google.com/presentation/d/1d5NtBn-qKS6AkUJ5CWhMNZArGX2_2esFoJ3R4L4-7VE)] |

## 1 Trees
### 1.1 Tree Definition
A tree consists of a set of **nodes** and a set of **edges** that connect those nodes, where there is **exactly one** path between any two nodes.

![graphs](graphs.png){:w="500"}
_Green structures below are trees. Pink ones are not._

### 1.2 Tree Traversals
Sometimes you want to iterate over a tree. What one might call "tree iteration" is actually called "**tree traversal**". Unlike lists, there are many orders in which we might visit the nodes.

![tree](tree.png){:w="350"}

#### Level Order
- DBFACEG

#### Depth First Traversals
![tree dfs](tree traversal algo.png)
_DBACFEG | ABCDEFG | ACBEGFD_

#### A Visual Trick
 We trace a path around the graph, from the top going counter-clockwise.
- Preorder traversal: "Visit" every time we pass the LEFT of a node. **1 2 4 5 7 8 3 6 9**
- Inorder traversal: "Visit" when you cross the BOTTOM of a node. **4 2 7 5 8 1 3 6 9**
- Postorder traversal: "Visit" when you cross the RIGHT a node. **4 7 8 5 2 9 6 3 1**

![trick](tree traversal.png){:w="350"}

### 1.3 Usefulness of Tree Traversals
#### Preorder Traversal for printing directory listing
![directory listing](directory listing.png){:w="450"}

#### Postorder Traversal for gathering file sizes
![file sizes](file sizes.png){:w="650"}
## 2 Graphs
### 2.1 Graph Definition
Trees are fantastic for representing strict hierarchical relationships. But not every relationship is hierarchical. A **graph** consists of a set of nodes and a set of zero or more edges, each of which connects two nodes. Note, all trees are graphs.

A **simple graph** is a graph with:
- No edges that connect a vertex to itself, i.e. no "length 1 loops".
- No two edges that connect the same vertices, i.e. no "parallel edges".

![simple graph](simple graph.png){:w="350"}
_Green graph below is simple, pink graphs are not._

In 61B, unless otherwise explicitly stated, all graphs will be simple.

### 2.2 Graph Types
![Graph Terms](graph terms.png){:w="500"}

### 2.3 Graph Problems
Some well known graph problems and their common names:
- **s-t Path**. Is there a path between vertices s and t?
- **Connectivity**. Is the graph connected, i.e. is there a path between all vertices?
- **Biconnectivity**. Is there a vertex whose removal disconnects the graph?
- **Shortest s-t Path**. What is the shortest path between vertices s and t?
- **Cycle Detection**. Does the graph contain any cycles?
- **Euler Tour**. Is there a cycle that uses every edge exactly once?
- **Hamilton Tour**. Is there a cycle that uses every vertex exactly once?
- **Planarity**. Can you draw the graph on paper with no crossing edges?
- **Isomorphism**. Are two graphs isomorphic (the same graph in disguise)?


## 3 Graph Traversals
### 3.1 Motivation: s-t Connectivity
Let’s solve a classic graph problem called the s-t connectivity problem.
Given source vertex `s` and a target vertex `t`, is there a path between `s` and `t`?

> A **path** is a sequence of vertices connected by edges.
{: .prompt-info}

![s-t connectivity](s-t connectivity.png){:w="400"}

One possible recursive algorithm for `connected(s, t)`.
```
Does s == t?
    If so, return true.
    Otherwise, if connected(v, t) for any neighbor v of s, return true.
Return false.
```
What is wrong with it? Can get caught in an infinite loop. Example:
```
connected(0, 7):
    Does 0 == 7? No, so...
    If (connected(1, 7)) return true;
connected(1, 7):
    Does 1 == 7? No, so…
    If (connected(0, 7)) … ← Infinite loop.
```
How do we fix it?

### 3.2 Depth First Search
Basic idea is same as before, but visit each vertex at most once. [[Demo](https://docs.google.com/presentation/d/1OHRI7Q_f8hlwjRJc8NPBUc1cMu5KhINH1xGXWDfs_dA/edit?usp=sharing)]
```
Mark s. ← Marking nodes prevents multiple visits
Does s == t?
    If so, return true.
    Otherwise, if connected(v, t) for any unmarked neighbor v of s, return true.
Return false.
```

#### Another example: DepthFirstPaths
Find a path from `s` to every other reachable vertex. [[Demo](https://docs.google.com/presentation/d/1lTo8LZUGi3XQ1VlOmBUF9KkJTW_JWsw_DOPq8VBiI3A/edit#slide=id.g76e0dad85_2_380)]
```
def dfs(v):
    Mark v.
    For each unmarked adjacent vertex w:
        set edgeTo[w] = v.
        dfs(w)
```

### 3.3 Tree vs. Graph Traversals
What we just did in `DepthFirstPaths` is called "**DFS Preorder**". i.e., **Action** (setting `edgeTo`) is before DFS calls to neighbors. One valid DFS preorder for this graph: 012543678, equivalent to the order of **dfs calls**.

![s-t connectivity](s-t connectivity.png){:w="400"}

We could also do actions in **DFS Postorder**. i.e., Action is after DFS calls to neighbors. Example:
```
dfs(s):
    mark(s)
    For each unmarked neighbor n of s:
        dfs(n)
    print(s)
```
Results for `dfs(0)` would be: 347685210, equivalent to the order of **dfs returns**.

Just as there are many tree traversals, so too are there many graph traversals:
- DFS Preorder: 012543678 (dfs calls).
- DFS Postorder: 347685210 (dfs returns).
- BFS order: Act in order of distance from `s`.
    - BFS stands for "breadth first search".
    - Analogous to "level order". Search is wide, not deep.
    - 0 1 24 53 68 7

## Summary
Graphs are a more general idea than a tree.
- A tree is a graph where there are no cycles and every vertex is connected.
- Key graph terms: Directed, Undirected, Cyclic, Acyclic, Path, Cycle.

Graph problems vary widely in difficulty.
- Common tool for solving almost all graph problems is traversal.
- A traversal is an order in which you visit / act upon vertices.
- Tree traversals:
    - Preorder, inorder, postorder, level order.
- Graph traversals:
    - DFS preorder, DFS postorder, BFS.
- By performing actions / setting instance variables during a graph (or tree) traversal, you can solve problems like s-t connectivity or path finding.
