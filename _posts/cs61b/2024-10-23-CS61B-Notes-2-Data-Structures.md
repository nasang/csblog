---
title: CS 61B (Sp21) Notes 2, Data Structures
categories: [UC Berkeley, CS61b Spring 2021]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/
---

## 13. Asymptotics I
Our goal is to somehow **<u>characterize the runtimes</u>** of two functions. Characterization should be (1) **simple** and **mathematically rigorous**; and (2) **demonstrate superiority** of one over the other.

<!-- Intuitive Runtime Characterizations
- Technique 1: Measure execution time in seconds using a client program.
- Technique 2A: Count possible operations for an array of size N = 10,000.
- Technique 2B: Count possible operations in terms of input array size N. -->

### 13.1 Asymptotic Analysis
#### 13.1.1 Scaling Matters
In most cases, we care only about **<u>asymptotic behavior</u>**, i.e. what happens for very large $N$.

Algorithms which scale well (e.g. look like lines) have better asymptotic runtime behavior than algorithms that scale relatively poorly (e.g. look like parabolas). We’ll informally refer to the “shape” of a runtime function as its **order of growth** (will formalize soon).
- Often determines whether a problem can be solved at all.

#### 13.1.2 Computing Worst Case Order of Growth (Tedious Approach)
- Construct a table of exact counts of all possible operations.

![count](13/count.png){: w="550" h="350"}

- Convert table into worst case order of growth using 4 simplifications:
1. Only consider the worst case.
2. Pick a representative operation (a.k.a. the cost model).
3. Ignore lower order terms.
4. Ignore multiplicative constants.
![simlipy](13/simplify.png){: w="500" h="600" }


#### 13.1.3 Computing Worst Case Order of Growth (Simplified Approach)
- Choose a representative operation to count (a.k.a. cost model).
- Figure out the order of growth for the count of the representative operation by either:
  - Making an exact count, then discarding the unnecessary pieces.
  - Using intuition and inspection to determine order of growth (only possible with lots of practice).


### 13.2 Asymptotic Notation

#### 13.2.1 Big-Theta (a.k.a. Order of Growth)
For some function $R(N)$ with **order of growth** $f(N)$, we write that $R(N) \in \Theta(f(N))$. There exists some positive constants $k_1$, $k_2$  such that $k_1 \cdot f(N) \leq R(N) \leq k_2 \cdot f(N)$, for all values $N$ greater than some $N_0$ (a very large $N$). 

![Big-Theta](13/Big-Theta.png){: w="450" h="300" }
*[Visualization](https://sp19.datastructur.es/materials/demos/asymptotics.html?rN=4*N%5E2+40*sin(N)&fN=N%5E2&k1=3&k2=5&maxN=15&maxY=1000)*

#### 13.2.2 Big-O
$R(N) \in O(f(N))$ means that there exists positive constant $k_2$ such that $R(N) \leq k_2 \cdot f(N)$, for all values of $N$ greater than some $N_0$ (a very large $N$).

![Big-O](13/Big-O.png){: w="450" h="300" }
*[Visualization](https://sp19.datastructur.es/materials/demos/asymptotics.html?rN=4*N%5E2+40*sin(N)&fN=N%5E4&k1=0&k2=5&maxN=15&maxY=1000)*

Whereas $\Theta$ can informally be thought of as something like “equals”, $O$ can be thought of as “less than or equal”.

### 13.3 Summary
Given a piece of code, we can express its runtime as a function $R(N)$, where $N$ is a property of the input of the function often representing the **size** of the input. Rather than finding the exact value of $R(N)$ , we only worry about finding the **order of growth** of $R(N)$. One approach (not universal):
- Choose a representative operation
- Let $C(N)$ be the count of how many times that operation occurs as a function of $N$
- Determine order of growth $f(N)$ for $C(N)$, i.e. $C(N) \in \Theta(f(N))$
- Often (but not always) we consider the worst case count
- If operation takes constant time, then $R(N) \in \Theta(f(N))$
- Can use $O$ as an alternative for $\Theta$. $O$ is used for upper bounds.

## 14. Disjoint Sets
```java
// The Disjoint Sets Interface
public interface DisjointSets {
    /** Connects two items P and Q. */
    void connect(int p, int q);
    /** Checks to see if two items are connected. */
    boolean isConnected(int p, int q);
}
```

### 14.1  Representation
![set_representation](14/set_representation.png)

给每个节点分配一个 parent，用树的形式表示。

- Find(x): 返回 x 的根节点，可以用 path compression 优化；（一般都优化）
- Union(x, y): 将 x 和 y 所属的树合并为一棵，方法是让 Find(x) 成为 Find(y) 的孩子。可以用 rank 优化，即总是让元素个数少的树连接到元素个数多的树下面。（一般不用优化）

两个优化都是为了让树的高度尽可能低。

### 14.2 Find
To find the root node of a given vertex.
**Path Compression**: When looking for the root node, connect all the nodes you come across on the way to the final root node, making them children of the root node.

Example: `find(13)`, `find(14)`

![path_compression1](14/path_compression1.png){: w="450" h="350"}

![path_compression2](14/path_compression2.png){: w="450" h="350"}

![path_compression3](14/path_compression3.png){: w="450" h="350"}

### 14.3 Union
To connect two vertices.

Example: `union(5, 2)`
```
find(5) -> 3
find(2) -> 0
make 3 a new child of 0
```

![basic_union](14/basic_union.png){: w="450" h="350"}

![basic_union2](14/basic_union2.png){: w="450" h="350"}

### 14.4 Union by Rank (**Weighted Quick Union**)
Modify quick-union to avoid tall trees. Track tree size (**number** of elements), and always link root of **smaller** tree **to** **larger** tree.

Example: `union(3, 8)`

![wqu1](14/wqu1.png){: w="550" h="350"}

![wqu2](14/wqu2.png){: w="550" h="350"}

Why Weights Instead of Heights? Worst case performance is asymptotically the same. Both are $\Theta(\log(N))$. Resulting code is more complicated with no performance gain.