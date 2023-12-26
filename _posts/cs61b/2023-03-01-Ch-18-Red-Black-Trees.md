---
title: CS61b 18. Red Black Trees
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/18
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [link](https://sp23.datastructur.es/index.html) | [Ch 18. Red Black Trees](https://cs61b-2.gitbook.io/cs61b-textbook/18.-red-black-trees) | [slide](https://docs.google.com/presentation/d/1GP_5y1hpfF7SfUz4rfwcP7uXIYZ4cNRREFyz6IDfWhg/edit?usp=sharing) |

Wonderfully balanced as they are, <u>B-Trees are really difficult to implement</u>. We need to keep track of the different nodes and the splitting process is pretty complicated. Let's find another way to create a balanced tree.

## 1 Tree Rotation
### 1.1 BSTs
Suppose we have a BST with the numbers `1`, `2`, `3`. Five possible BSTs.
- The specific BST you get is based on the insertion order.
- More generally, for $N$ items, there are [Catalan(N)](https://en.wikipedia.org/wiki/Catalan_number) different BSTs.

![BSTs](BSTs.png){:w="550" h="250"}

Given any BST, it is possible to move to a different configuration using “rotation”.
- In general, can move from any configuration to any other in 2n - 6 rotations (see [Rotation Distance, Triangulations, and Hyperbolic Geometry](https://www.cs.cmu.edu/~sleator/papers/rotation-distance.pdf) or [Amy Liu](https://medium.com/@liuamyj/its-triangles-all-the-way-down-part-1-17f932f4c438)).

### 1.2 Definition
#### rotateLeft
`rotateLeft`(G): Let x be the right child of G. Make G the new left child of x.

![rotate_left_1](rotate_left_1.png){:w="550" h="400"}

- Can think of as temporarily merging G and P, then sending G down and left.

![rotate_left](rotate_left.png){:w="550" h="250"}

#### rotateRight
`rotateRight`(P): Let x be the left child of P. Make P the new right child of x.
- Can think of as temporarily merging G and P, then sending P down and right.

![rotate_right](rotate_right.png){:w="550" h="250"}

### 1.3 Tree Balancing
Rotation:
- Can shorten (or lengthen) a tree.
- Preserves search tree property.

![rotation](rotation.png){:w="600" h="250"}

Rotation allows balancing of a BST in $O(N)$ moves.

## 2 Left Leaning Red-Black Trees (LLRBs)
### 2.1 The 2-3 Tree Isometry
2-3 trees always remain balanced, but they are very hard to implement. On the other hand, BSTs can be unbalanced, but are simple and intuitive. Is there a way to combine the best of two worlds? Why not create <u>a tree that is implemented using a BST, but is structurally identical to a 2-3 tree and thus stays balanced</u>?

**Representing a 2-3 Tree as a BST**

A 2-3 tree with only 2-nodes is trivial. BST is exactly the same.

![2-nodes](2-nodes.png){:w="550" h="250"}

**Dealing with 3-Nodes**

- Possibility 1: Create dummy “glue” nodes.

![dummy_glue_node](dummy_glue_node.png){:w="550" h="400"}
_Result is inelegant. Wasted link. Code will be ugly._

- Possibility 2: Create “glue” links with the smaller item **<u>off to the left</u>**.
    - For convenience, we’ll mark glue links as “**<font color=red>red</font>**”.

![glue_link](glue_link.png){:w="550" h="400"}
_Idea is commonly used in practice (e.g. java.util.TreeSet)._

A BST with left glue links that represents a 2-3 tree is often called a “Left Leaning Red Black Binary Search Tree” or LLRB.

### 2.2 LLRB Properties
- Suppose we have a 2-3 tree of height H. The maximum height of the corresponding LLRB is
  - H (black) + <font color=red>H + 1 (red)</font> = 2H + 1.

![LLRB](LLRB.png){:w="550" h="400"}

- No node has two red links [otherwise it’d be analogous to a 4 node, which are disallowed in 2-3 trees].
- Every path from root to null has same number of **<u>black links</u>** [because 2-3 trees have the same number of links to every leaf]. LLRBs are therefore balanced.

### 2.3 Maintaining Isometry with Rotations
When inserting into an LLRB tree, we <u>always insert the new node with a red link to its parent node</u>. This is because in a 2-3 tree, we are always inserting by adding to a leaf node, the color of the link we add should always be red.

But sometimes, inserting red links at certain places might lead to cases where we break one of the invariants of LLRBs. Below are three such cases where we need to perform certain tasks address in order to maintain the LLRB tree's proper structure.

**Case 1: Insertion on the Right**

- If the left child is also a red link, go to case 3.
- Otherwise, Rotate `E` left.

![case1](case1.png){:w="550" h="300"}

**Case 2: Double Insertion on the Left**

- Rotate `Z` right. Then go to case 3.

![case2](case2.png){:w="550" h="300"}

**Case 3: Node has Two Red Children**

- Flip the colors of all edges touching `B`.

![case3](case3.png){:w="350" h="300"}

**Cascading operations**

It is possible that a rotation or flip operation will cause an additional violation that needs fixing.

### 2.4 Runtime and Implementation
The runtime analysis for LLRBs is simple if you trust the 2-3 tree runtime.
- LLRB tree has height $O(\log N)$.
- Contains is trivially $O(\log N)$.
- Insert is $O(\log N)$.
  - $O(\log N)$ to add the new node.
  - $O(\log N)$ rotation and color flip operations per insert.

Amazingly, turning a BST into an LLRB requires only 3 clever lines of code.

![imp](imp.png){:w="600" h="450"}
