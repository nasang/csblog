---
title: CS61b 21. Heaps and Priority Queues
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/21
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [link](https://sp23.datastructur.es/index.html) | [Ch 21. Heaps and Priority Queues](https://cs61b-2.gitbook.io/cs61b-textbook/21.-heaps-and-priority-queues) | [slide](https://docs.google.com/presentation/d/1uws16IyE3oGKKbKgTpl7s4a2950ef2CjI7B3ez8Gctw/) |

## 1 Priority Queue

### 1.1 The Priority Queue Interface
```java
/** (Min) Priority Queue: Allowing tracking and removal of the
  * smallest item in a priority queue. */
public interface MinPQ<Item> {
	/** Adds the item to the priority queue. */
	public void add(Item x);
	/** Returns the smallest item in the priority queue. */
	public Item getSmallest();
	/** Removes the smallest item from the priority queue. */
	public Item removeSmallest();
	/** Returns the size of the priority queue. */
	public int size();
}
```
Useful if you want to keep track of the “smallest”, “largest”, “best” etc. seen so far.

### 1.2 Using a PQ
Suppose we have a particle detector that records the energy of incoming particles. We want to record the $M$ highest energy particles in a given day.

```java
public List<Particle> highestEnergyParticles(Detector det, int M) {
    	Comparator<Particle> cmptr = new EnergyComparator();
    	MinPQ<Particle> highEnergyParticles = new HeapMinPQ<>(cmptr);
    	for (Timer timer = new Timer(); timer.hours() < 24; ) {
        	highEnergyParticles.add(det.getNextParticle());
        if (highEnergyParticles.size() > M)
           { highEnergyParticles.removeSmallest(); }
    }
    ArrayList<String> returnList = new ArrayList<String>();
    	while (highEnergyParticles.size() > 0) {
            returnList.add(highEnergyParticles.removeSmallest());
    	}
    	return returnList;
}
```

## 2 Heaps

### 2.1 Heap Structure
BSTs would work, but need to be kept bushy and duplicates are awkward.

Binary min-heap: Binary tree that is complete and obeys min-heap property.
- **Min-heap**: Every node is less than or equal to both of its children.
- **Complete**: Missing items (if any) only at the bottom level, all nodes are as far left as possible.

![heap](heap.png)

### 2.2 Heap Operations
#### 2.2.1 Heap Add
> Algorithm for `add(x)`: to maintain the **completeness**, the natural thought is to place `x` in the leftmost empty spot on the lowest level. However, this doesn't guarantee the **min-heap** property, so further adjustments are needed.
{: .prompt-tip}

> `swim`: Continually compare `x` with its parent. If `x` is smaller, swap it with the parent. Repeat this process until `x` is in the right position.
{: .prompt-info}

<img src="insert.png" alt="swim" style="zoom:100%;" />

#### 2.2.2 Heap Delete
> Algorithm for `removeSmallest()`: to maintain the completeness of the structure, the intuitive approach is to swap the root node with the node at the end, then remove it. However, this doesn't guarantee the min-heap property, so further adjustments are needed.
{: .prompt-tip}

> `sink`: Continually compare `x` with its left and right nodes. Swap x with the smaller of the two nodes. Repeat this process until `x` is in the right position.
{: .prompt-info}

<img src="remove.png" alt="sink" style="zoom:80%;" />

**Heap Operations Summary**
- `getSmallest()` - return the item in the root node.
- `add(x)` - place the new employee in the last position, and promote as high as possible.
- `removeSmallest()` - assassinate the president (of the company), promote the rightmost person in the company to president. Then demote repeatedly, always taking the ‘better’ successor.


### 2.3 Tree Representation
How do we Represent a Tree in Java?

<img src="tree.png" alt="tree" style="zoom:50%;" />

#### Approach 1a, 1b, and 1c
Create mapping from node to children.

![ap1digrams](ap1digrams.png){: w="550" h="250"}

![ap1codes](ap1codes.png){: w="550" h="250"}

#### Approach 2
Store keys in an array. Store parentIDs in an array.
- Similar to what we did with disjointSets.

<img src="ap2-1.png" alt="ap2-1" style="zoom: 40%;" />

A more complex example：

![ap2-2](ap2-2.png){: w="550" h="250"}

#### Approach 3
Store keys in an array. Don’t store structure anywhere.

In Approach2, we observe that when numbering a binary tree in the order of level traversal, if the tree has the property of being complete, there is a pattern between the parent and child node numbers: the parent number corresponding to the node number $k$ is $\frac{k-1}{2}$.

Further, if we leave the 0th position empty, the following patterns emerge:
- `leftChild(k)` $= 2k$
- `rightChild(k)` $= 2k + 1$
- `parent(k)` = $\frac{k}{2}$

![ap3](ap3.png)

<!-- ## Implementation Consideration

### The Implementation

- You may find the [Princeton implementation of a heap](http://algs4.cs.princeton.edu/24pq/MinPQ.java.html) useful. [Here](https://algs4.cs.princeton.edu/24pq/) is a more detailed explanation of each of the methods.

- My implementation based on Sp18 lab10 is [Here](https://github.com/nsang202/skeleton-sp18/blob/master/lab10/ArrayHeap.java).
- A record of Sp18 lab10 will be released soon! -->
