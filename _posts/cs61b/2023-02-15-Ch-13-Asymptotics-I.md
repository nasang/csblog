---
title: CS61b 13. Asymptotics I
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/13
---

## 1 Goal: Measuring Code Efficiency
Our goal is to somehow **<u>characterize the runtimes</u>** of two functions.
- Characterization should be **simple** and **mathematically rigorous**.
- Characterization should **demonstrate superiority** of one over the other.

## 2 Intuitive Runtime Characterizations
- Technique 1: Measure execution time in seconds using a client program.
- Technique 2A: Count possible operations for an array of size N = 10,000.
- Technique 2B: Count possible operations in terms of input array size N.

## 3 Asymptotic Analysis

### 3.1 Why Scaling Matters

In most cases, we care only about **<u>asymptotic behavior</u>**, i.e. what happens for very large N.

Algorithms which scale well (e.g. look like lines) have better asymptotic runtime behavior than algorithms that scale relatively poorly (e.g. look like parabolas).

We’ll informally refer to the “shape” of a runtime function as its **order of growth** (will formalize soon).
- Effect is dramatic! Often determines whether a problem can be solved at all.

### 3.2 Computing Worst Case Order of Growth (Tedious Approach)
- Construct a table of exact counts of all possible operations.

![count](count.png){: w="550" h="350"}

- Convert table into worst case order of growth using 4 simplifications:
1. Only consider the worst case.
2. Pick a representative operation (a.k.a. the cost model).
3. Ignore lower order terms.
4. Ignore multiplicative constants.
![simlipy](simplify.png){: w="500" h="600" }


### 3.3 Computing Worst Case Order of Growth (Simplified Approach)
- Choose a representative operation to count (a.k.a. cost model).
- Figure out the order of growth for the count of the representative operation by either:
  - Making an exact count, then discarding the unnecessary pieces.
  - Using intuition and inspection to determine order of growth (only possible with lots of practice).


## 4 Asymptotic Notation

### 4.1 Big-Theta (a.k.a. Order of Growth)
For some function $R(N)$ with **order of growth** $f(N)$, we write that:

$$
R(N) \in \Theta(f(N))
$$

and there exists some positive constants $k_1$, $k_2$  such that

$$
k_1 \cdot f(N) \leq R(N) \leq k_2 \cdot f(N)
$$

 for all values $N$ greater than some $N_0$ (a very large $N$). [Visualization](https://sp19.datastructur.es/materials/demos/asymptotics.html?rN=4*N%5E2+40*sin(N)&fN=N%5E2&k1=3&k2=5&maxN=15&maxY=1000)

![Big-Theta](Big-Theta.png){: w="500" h="300" }

### 4.2 Big-O
$R(N) \in O(f(N))$ means that there exists positive constant $k_2$ such that:

$$
R(N) \leq k_2 \cdot f(N)
$$

 for all values of $N$ greater than some $N_0$ (a very large $N$). [Visualization](https://sp19.datastructur.es/materials/demos/asymptotics.html?rN=4*N%5E2+40*sin(N)&fN=N%5E4&k1=0&k2=5&maxN=15&maxY=1000)

![Big-O](Big-O.png){: w="500" h="300" }

Whereas $\Theta$ can informally be thought of as something like “equals”, $O$ can be thought of as “less than or equal”.

## Summary
Given a piece of code, we can express its runtime as a function $R(N)$
- $N$ is a property of the input of the function often representing the **size** of the input

Rather than finding the exact value of $R(N)$ , we only worry about finding the **order of growth** of $R(N)$.

One approach (not universal):
- Choose a representative operation
- Let $C(N)$ be the count of how many times that operation occurs as a function of $N$
- Determine order of growth $f(N)$ for $C(N)$, i.e. $C(N) \in \Theta(f(N))$
- Often (but not always) we consider the worst case count
- If operation takes constant time, then $R(N) \in \Theta(f(N))$
- Can use $O$ as an alternative for $\Theta$. $O$ is used for upper bounds.
