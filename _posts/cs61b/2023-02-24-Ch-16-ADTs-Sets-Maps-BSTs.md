---
title: CS61b 16. ADTs, Sets, Maps, BSTs
categories: [UC Berkeley, CS61b Spring 2023]
tags: [data structures]
author: <na_id>
math: true
img_path: /assets/img/cs61b/16
---

| Course website| Reading | Lecture |
|:--------|:--------|:--------|
| [link](https://sp23.datastructur.es/index.html) | [Ch 16](https://cs61b-2.gitbook.io/cs61b-textbook/16.-adts-and-bsts) | [slide](https://docs.google.com/presentation/d/1vf9x_H6lhzBMmkdd4jle46W3KFthWaM-sDaNvn604eE/edit#slide=id.g21148fb6dae_0_360) |

## 1 Abstract Data Types
An **Abstract Data Type (ADT)** is defined only by its operations, not by its implementation.

The built-in [**java.util**](https://docs.oracle.com/javase/8/docs/api/java/util/package-summary.html) package provides a number of useful:
- **Interfaces**: ADTs (List, Set, Map, etc.) and other stuff.
- **Implementations**: Concrete classes you can use.

```java
List<Integer> L = new ArrayList<>();
```

![java](Java_utils.png){: w="550" h="350"}
_Common interfaces in Java and their implementations_

This lecture is about the basic ideas behind the `TreeSet` and `TreeMap`.

## 2 Binary Search Trees

### 2.1 Derivation
In an earlier lecture, we implemented a set based on unordered arrays. For the **ordered linked list** set implementation below,  `contains` and `add` take worst case linear time, i.e. $\Theta(N)$.

![ordered_linked_list](ordered linked list.png){: w="550" h="350"}

**Fundamental Problem: Slow search, even though it’s in order.**

In **binary search**, we know the list is sorted, so we can use this information to narrow our search.

<u>Applying binary search to a linked list</u> might seem challenging at first. We need to traverse all the way to the middle to check the element there, which would take linear time.

However, we can optimize this process. One way is to **keep a reference to the middle node**. This allows us to reach the middle in constant time. Additionally, if we **reverse the nodes' pointers**, we can traverse both the left and right halves of the list, effectively halving our runtime. We can further optimize by **adding pointers to the middle of each recursive half like so**.

![halved](halved.png){: w="550" h="350"}
_A linked list with a middle pointer_

![better](better.png){: w="550" h="350"}
_A linked list with recursive middle pointers is a binary tree_

Now, if you stretch this structure vertically, you will see a tree. This specific tree is called a **binary tree** because each juncture splits in 2.

### 2.2 BST Definition
A **binary search tree** is a rooted binary tree with the BST property.

> **BST Property**  For every node `X` in the tree:
- Every key in the left subtree is less than `X`’s key.
- Every key in the right subtree is greater than `X`’s key.
{: .prompt-info}

```java
private class BST<Key> {
    private Key key;
    private BST left;
    private BST right;

    public BST(Key key, BST left, BST Right) {
        this.key = key;
        this.left = left;
        this.right = right;
    }

    public BST(Key key) {
        this.key = key;
    }
}
```


### 2.3 Contains
To find a searchKey in a BST, we employ binary search, which is made easy due to the BST property.
```java
static BST find(BST T, Key sk) {
   if (T == null)
      return null;
   if (sk.equals(T.key))
      return T;
   else if (sk < T.key)
      return find(T.left, sk);
   else
      return find(T.right, sk);
}
```

The runtime to complete a search on a “bushy” BST in the worst case is $\Theta(\log N)$, where $N$ is the number of nodes.

### 2.4 Insert
We **always** insert at a leaf node.

```java
static BST insert(BST T, Key ik) {
  if (T == null)
    return new BST(ik);
  if (ik < T.key)
    T.left = insert(T.left, ik);
  else if (ik > T.key)
    T.right = insert(T.right, ik);
  return T;
}
```

### 2.5 Deletion
Deleting from a binary tree is a little bit more complicated because whenever we delete, we need to make sure we **reconstruct the tree and still maintain its BST property**. Let's break this problem down into three categories:
- the node we are trying to delete has no children
- has 1 child
- has 2 children

#### Case 1: Key with no Children
We can just <u>delete its parent pointer</u> and the node will eventually be swept away by the garbage collector.

#### Case 2: Key with one Child
The child maintains the BST property with the parent of the node because the property is recursive to the right and left subtrees. Therefore, we can just <u>reassign the parent's child pointer to the node's child</u> and the node will eventually be garbage collected.

Example: **delete("flat")**

![one child](del_1_child.png){: w="300" h="300"}

<!-- Goal:
- Maintain BST property.
- `flat`’s child definitely larger than `dog`.
  - Safe to just move that child into `flat`’s spot.

Thus: Move `flat`’s parent’s pointer to `flat`’s child.
- `flat` will be garbage collected (along with its instance variables). -->


#### Case 3: Key with two Children (Hibbard)
If the node has two children, the process becomes a little more complicated because we can't just assign one of the children to be the new root. This might break the BST property.
Instead, we <u>choose a new node to replace the deleted one</u>.
We know that the new node must:
- be > than everything in left subtree.
- be < than everything right subtree.

Example: **delete("dog")**


![two children](del_2_children.png){: w="300" h="300"}

Goal:
- Find a new root node.
    - Must be > than everything in left subtree.
    - Must be < than everything right subtree.

Choose either <u>predecessor</u> (`cat`, the right-most node in the left subtree) or <u>successor</u> (`elf`, the left-most node in the right subtree).
- Delete `cat` or `elf`, and stick new copy in the root position:
    - This deletion guaranteed to be either case 1 or 2.

This strategy is known as **Hibbard deletion**.

## 3 Sets and Maps
**Set**

Can think of the BST below as representing a Set:
- {mo, no, sumomo, uchi, momo}

![set](set.png){: w="550" h="250}

**Map**

To represent maps, just have each BST node store key/value pairs.

![map](map.png){: w="550" h="250}

Note: No efficient way to look up by value.
- Example: Cannot find all the keys with value = 1 without iterating over **ALL** nodes. This is fine.

## Summary
- Abstract data types (ADTs) are defined in terms of operations, not implementation.Several useful ADTs:
Disjoint Sets, Map, Set, List.
- Java provides Map, Set, List interfaces, along with several implementations.
- We’ve seen two ways to implement a Set (or Map):
  - ArraySet: $\Theta(N)$ operations in the worst case.
  - BST: $\Theta(\log N)$ operations if tree is balanced.
- BST Implementations:
  - Search and insert are straightforward (but insert is a little tricky).
  - Deletion is more challenging. Typical approach is “Hibbard deletion”.
