---
title: CS61b 24. Shortest Paths
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/24
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [CS 61B Spring 2023](https://sp23.datastructur.es/index.html) | [Ch 24. Shortest Paths](https://cs61b-2.gitbook.io/cs61b-textbook/24.-shortest-paths) |[[video (2019)](https://www.youtube.com/watch?v=iMoFtG1md3w&feature=youtu.be&t=5m03s)] [[slides (2019)](https://docs.google.com/presentation/d/1X_HRo2Wr9FwFrzRcH8Ppq6L5r1bEGrlhLS2L9hY4-Ec/edit#slide=id.g54762a0157_3_13)] [[slides](https://docs.google.com/presentation/d/18pU3NjOeYZCWqo7VrXZwsF2y7AEO4X7xY50NnsKyHw0/edit#slide=id.g2195edd1ad8_0_135)]|

## 1 Introduction
### 1.1 Why BFS Doesn't Work
BFS would not be a good choice for a google maps style navigation application. BFS returns path with shortest **number of edges**, not necessarily the shortest path.

![BFS Example](BFS Example.png){:w="550"}
_BFS yields a route of length ~330 m instead of ~150 m._

We need an algorithm that takes into account edge distances, also known as **edge weights**.

### 1.2 The Shortest Paths Tree
#### Single Source Single Target Shortest Path
![Single Source Single Target](Single Source Single Target.png){:w="450"}
_Find the shortest paths from source vertex s to some target vertex t._

Observation: Solution will always be a path with no cycles (assuming non-negative weights).

#### Single Source Shortest Paths
![Single Source](Single Source.png){:w="450"}
_Find the shortest paths from source vertex s to every other vertex._

Observation: Solution will always be a **tree**. (Can think of as the union of the shortest paths to all vertices.) This is so-called **Shortest Paths Tree** (SPT).
## 2 Dijkstra's Algorithm
### 2.1 Algorithm
We can find the SPT using Dijkstra’s algorithm.

>**Dijkstra’s algorithm** Perform a **best first search**: Visit vertices in **order of best-known distance** from source. On visit, ***relax*** every edge from the visited vertex. [[Demo](https://docs.google.com/presentation/d/1_bw2z1ggUkquPdhl7gwdVBoTaoJmaZdpkV6MoAgxlJc/pub?start=false&loop=false&delayms=3000&slide=id.g771336078_0_180)]
{:.prompt-tip}

> **Relax**: If we find a better path, update our preferred edge and distance
{:.prompt-info}

```
Dijkstra:
    pq = PriorityQueue()
    pq.add((s, 0))
    while pq:
        v, dis = pq.pop()
        for (x, weight) in v.neighbors:
            # relax ⬇️
            if dis + weight < distTo[x]:
                distTo[x] = dis + weight
                edgeTo[x] = v
                pq.add((x, distTo[x]))
            # relax ⬆️
```

### 2.2 Correctness
Dijkstra’s is guaranteed to return a correct result if all edges are non-negative.

## 3 A* Algorithm
If we have only a single target in mind, A* algorithm can do better:
>**A* Algorithm** Visit vertices in order of `d(source, v) + h(v, goal)`, where `h(v, goal)` is an estimate of the distance from v to our goal. [[Demo](https://docs.google.com/presentation/d/177bRUTdCa60fjExdr9eO04NHm0MRfPtCzvEup1iMccM/edit#slide=id.g771336078_0_180)]
{:.prompt-tip}
