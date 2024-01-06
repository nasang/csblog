---
title: CS61b 25. Minimum Spanning Trees
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/25
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [CS 61B Spring 2023](https://sp23.datastructur.es/index.html) | [Ch 25. Minimum Spanning Trees](https://cs61b-2.gitbook.io/cs61b-textbook/25.-minimum-spanning-trees) |[[video (2018)](https://www.youtube.com/watch?v=vnKK38JS9Ik&list=PL8FaHk7qbOD7SvlWei_-neNmgmXzJG2ad&index=2&ab_channel=JoshHug)] [[slides](https://docs.google.com/presentation/d/1ILecDfqGbGg676xEmp4djysXv2nPNKOZ8FcqjlnR9fE/edit#slide=id.g21e4ffc69c9_0_2)]|

## 1 Minimum Spanning Trees
### 1.1 Intro
Given an **undirected** graph `G`, a ***spanning tree*** `T` is a subgraph of `G`, where `T`:
- Is connected and acyclic. (make it a tree)
- Includes all of the vertices. (makes it spanning)

![MST](MST.png){:w="300"}

A ***minimum spanning tree*** (MST) is a spanning tree of minimum total weight.

### 1.2 The Cut Property
- A **cut** is an assignment of a graph’s nodes to two non-empty sets.
- A **crossing edge** is an edge which connects a node from one set to a node from the other set.

![Cut Property](Cut Property.png){:w="350"}

> **Cut property**: Given any cut, minimum weight crossing edge is in the MST. (assume edge weights are unique)
{:.prompt-info}

### 1.3 Cut Property Proof
Suppose that the minimum crossing edge `e` were not in the MST.
- Adding `e` to the MST creates a cycle.
- Some other edge `f` must also be a crossing edge.
- Removing `f` and adding `e` is a lower weight spanning tree.
Contradiction!

![Cut Property Proof](Cut Property Proof.png){:w="350"}

## 2 Prim’s Algorithm
### 2.1 Concept
> **Prim’s Algorithm**: Add shortest edge (mark black) that has one node inside the MST under construction. Repeat until V-1 edges. [[Demo](https://docs.google.com/presentation/d/1NFLbVeCuhhaZAM1z3s9zIYGGnhT4M4PWwAc-TLmCJjc/edit#slide=id.g9a60b2f52_0_0)]
{:.prompt-tip}

### 2.2 Implementation
Insert all vertices into fringe `PQ`, storing vertices in order of distance from tree.
Repeat: Remove (closest) vertex `v` from `PQ`, and relax all edges pointing from `v`. [[Demo](https://docs.google.com/presentation/d/1GPizbySYMsUhnXSXKvbqV4UhPCvrt750MiqPPgU-eCY/edit#slide=id.g9a60b2f52_0_0)]

## 3 Kruskal’s Algorithm
### 3.1 Concept
> **Kruskal’s Algorithm**:Consider edges in order of increasing weight. Add to MST unless a cycle is created. Repeat until V-1 edges. [[Demo](https://docs.google.com/presentation/d/1RhRSYs9Jbc335P24p7vR-6PLXZUl-1EmeDtqieL9ad8/edit#slide=id.g9a60b2f52_0_0)]
{:.prompt-tip}

### 3.2 Implementation
Insert all edges into `PQ`.
Repeat: Remove smallest weight edge. Add to MST if no cycle created. (Weighted Quick Union UF) [[Demo](https://docs.google.com/presentation/d/1KpNiR7aLIEG9sm7HgX29nvf3yLD8_vdQEPa0ktQfuYc/edit?usp=sharing)]

> **WQU**: For each edge, check if the two vertices are connected.
If not, union them.
If so, there is a cycle.
