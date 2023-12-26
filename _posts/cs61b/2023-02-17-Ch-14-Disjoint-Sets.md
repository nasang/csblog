---
title: CS61b 14. Disjoint Sets
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/14
---

## 1 The Disjoint Sets Interface

```java
public interface DisjointSets {
	/** Connects two items P and Q. */
	void connect(int p, int q);

	/** Checks to see if two items are connected. */
	boolean isConnected(int p, int q);
}
```

## 2 Representation
Example: **connect(5, 2)**

![set representation](set representation.png)

给每个节点分配一个 parent，用树的形式表示。

- Find(x): 返回 x 的根节点，可以用 path compression 优化；（一般都优化）
- Union(x, y): 将 x 和 y 所属的树合并为一棵，方法是让 Find(x) 成为 Find(y) 的孩子。可以用 rank 优化，即总是让元素个数少的树连接到元素个数多的树下面。（一般不用优化）

两个优化都是为了让树的高度尽可能低。

### 2.1 Find
To find the root node of a given vertex.

#### 2.1.1 Path Compression
When looking for the root node, connect all the nodes you come across on the way to the final root node, making them children of the root node.

Example: **find(13)**, **find(14)**
```
find(13)
    parent[13] -> 6
    parent[13] <- find(6)
        parent[6] -> 1
        parent[6] <- find(1)
            parent[1] -> 0
            parent[1] <- find(0)
                parent[0] -> 0
                return 0
            return parent[1] <- 0
        return parent[6] <- 0
    return parent[13] <- 0
```

![path compression1](path compression1.png){: w="550" h="350"}

![path compression2](path compression2.png){: w="550" h="350"}

![path compression3](path compression3.png){: w="550" h="350"}

### 2.2 Union
To connect two vertices.

#### 2.2.1 Basic Union
Example: **union(5, 2)**
```
find(5) -> 3
find(2) -> 0
make 3 a new child of 0
```

![basic union](basic union.png){: w="550" h="350"}

![basic union2](basic union2.png){: w="550" h="350"}

#### 2.2.2 Union by Rank (**Weighted Quick Union**)
> Modify quick-union to avoid tall trees.
- Track tree size (**number** of elements).
- New rule: Always link root of **smaller** tree **to** **larger** tree.
{: .prompt-tip}

Example: **union(3, 8)**

![wqu1](wqu1.png){: w="550" h="350"}

![wqu2](wqu2.png){: w="550" h="350"}

- 为什么用元素的个数，而不是树的高度？
    - 因为最坏的情况下都是 $ Θ(\log(N)) $，而使用高度会使得代码更复杂。

## 3 Code Implementation

```python
class UnionFind:
    # Constructor of Union-find. The size is the length of the root array.
    def __init__(self, size):


    def find(self, x): # optimized with path compression
        if self.parent[x] == x:
            return x
        self.parent[x] = self.find(self.parent[x])
        return self.parent[x]


    def union(self, x, y): # basic version
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX != rootY:
            self.parent[rootY] = rootX


    def isConnected(self, x, y):
        return self.find(x) == self.find(y)
```

- The `union` function – Optimized by union by rank:

  ```python
  def union(self, x, y):
      rootX = self.find(x)
      rootY = self.find(y)
      if rootX != rootY:
          if self.rank[rootY] < self.rank[rootX]:
              self.parent[rootY] = rootX
          elif self.rank[rootX] < self.rank[rootY]:
              self.parent[rootX] = rootY
          else:
              self.parent[rootY] = rootX
              self.rank[rootX] += 1
  ```
