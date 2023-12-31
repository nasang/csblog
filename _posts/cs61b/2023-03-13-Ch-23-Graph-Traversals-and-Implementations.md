---
title: CS61b 23. Graph Traversals and Implementations
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/23
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [CS 61B Spring 2023](https://sp23.datastructur.es/index.html) | [Ch 23. Graph Traversals and Implementations](https://cs61b-2.gitbook.io/cs61b-textbook/23.-graph-traversals-and-implementations) |[[video (2022)](https://youtu.be/YgPtwbWpLaM)] [[slides](https://docs.google.com/presentation/d/14JBHhFswzC_9_g_OD0DARiLTE5J7wZJ4hIQJQCvBB6c)] [[slides (2022)](https://docs.google.com/presentation/d/11iacyiFt3QUrzo1yAU_xoXAjGTH4UzV7o6CR04HYRrI/edit#slide=id.g409413421_0637)]|

## 1 Breadth First Search
### 1.1 Shortest Paths Challenge
Given the graph above, find the shortest path from s to all other vertices.

BFS Answer [[Demo](https://docs.google.com/presentation/d/1JoYCelH4YE6IkSMq_LfTJMzJ00WxDj7rEa49gYmAtc4/edit?usp=sharing)]
```py
def bfs():
    fringe = deque([s])
    while fringe:
        v = fringe.popleft()
        for w in v.neighbors:
            if not marked[w]:
                marked[w] = True
                edgeTo[w] = v
                # distTo[w] = distTo[v] + 1
            fringe.append(w)
```

## 2 Graph Representations
### 2.1 Adjacency Matrix
![adj matrix](adjacency matrix.png){:w="450"}

DFS, BFS Runtime: $O(V^2)$

### 2.2 Edge Sets
![edge sets](Edge Sets.png){:w="450"}

### 2.3 Adjacency Lists
Common approach: Maintain array of lists indexed by vertex number. Most popular approach for representing graphs. Efficient when graphs are "sparse" (not too many edges).

![adj lists](Adjacency Lists.png){:w="400"}

DFS, BFS Runtime: $O(V+E)$
